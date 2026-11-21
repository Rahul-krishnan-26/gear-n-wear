# GEAR N WEAR storefront

A Next.js storefront and Super Admin dashboard backed by PostgreSQL.

## Run locally

1. Install Node.js 20.9+ and create a PostgreSQL database for this site.
2. Copy `.env.example` to `.env.local`. Set `DATABASE_URL` to the database connection string. The database role needs permission to create and update tables. Set `ADMIN_PASSWORD` to a password of at least 12 characters and `COOKIE_SECRET` to a private random value of at least 32 characters.
3. Run `npm ci` and `npm run dev`. Open the URL printed by Next.js. The app creates its tables and populates the demo catalog on the first visit to an empty database.

## Manage the store

- Open `/admin`. In **Storefront → Navigation menu**, use **Add link** or **Remove**, or edit a link in place. Enter a store path such as `/shop`, then save. Both desktop and mobile use the saved menu.
- In **Products**, use **New product**, **Edit**, or **Delete**. Product categories and collections are populated from the catalog. In **Orders**, review orders and update fulfillment.
- Customer checkout uses cash on delivery. Orders reserve stock in PostgreSQL and can be tracked with the order number and checkout email. Online payment is available when Razorpay is configured below.
- For seller information on printable order documents, set `STORE_LEGAL_NAME`, `STORE_BILLING_ADDRESS`, `STORE_GSTIN` and `STORE_GST_STATE` using your registered business details. Without seller details, the printable document is an order summary.
- The demo database and company profile contain sample catalog and marketing data. Replace these with your business details before accepting customer orders. See `GOOGLE_APPS_SCRIPT_SETUP.md` for optional Gmail notifications.

For Node.js production hosting, set the environment variables on the host and run `npm ci`, `npm run build`, then `npm start` behind HTTPS. Set `NEXT_PUBLIC_SITE_URL` to the production address if sending notifications with links. Super Admin always requires credentials in production.

## Deploy to Vercel with Supabase

1. Import the source into a Next.js project in Vercel. Configure the environment variables before the first deployment.
2. Use the PostgreSQL connection string from **Connect** in the Gear & Wear Supabase project. Keep `DATABASE_URL`, `ADMIN_PASSWORD`, and `COOKIE_SECRET` server-side. A Supabase API key cannot replace the PostgreSQL connection string.
3. Use a dedicated schema for storefront tables, and keep it out of Supabase's exposed schemas. Have the database administrator provision a storefront login role that owns that schema and configure the role's default `search_path` to that schema. The app creates its own tables and foreign keys in the connection's current schema. For an installed storefront, keep its existing schema unless you are migrating its data.
4. Open `/api/health` after deploying to confirm the database connection, then open `/admin` and sign in. Replace the sample catalog and business details before accepting live orders.

[Supabase PostgreSQL connection guide](https://supabase.com/docs/guides/database/connecting-to-postgres)

## Razorpay online payments

Cash on delivery works without gateway keys. Add these environment variables in the hosting dashboard to enable hosted online checkout.

| Variable | Value |
| --- | --- |
| `RAZORPAY_KEY_ID` | Razorpay key ID. Use a test key on a private staging deployment. |
| `RAZORPAY_KEY_SECRET` | Matching API key secret. Store as a server-side secret. |
| `RAZORPAY_WEBHOOK_SECRET` | Secret configured for this deployment's Razorpay webhook. |
| `SITE_URL` | Canonical HTTPS storefront URL. |

In the same Razorpay mode as the keys, set the webhook destination to `https://YOUR-STOREFRONT/api/payments/razorpay/webhook` and subscribe to `payment_link.paid`, `payment_link.expired`, and `payment_link.cancelled`. Use a separate deployment and database when using test keys. Place a Razorpay test order and verify that it moves from Awaiting Payment to Confirmed before enabling live keys. Razorpay controls the methods available on the hosted payment page.

Payment links reserve stock for 30 minutes. Signed expiry or cancellation webhooks release stock. For recovery if an attempt fails before a link exists, periodically call `GET /api/cron?job=payments` using `Authorization: Bearer $CRON_SECRET`. Manage online refunds in the Razorpay Dashboard. Online orders retain their payment records in the storefront.

[Official payment link webhook setup](https://razorpay.com/docs/webhooks/payment-links)
