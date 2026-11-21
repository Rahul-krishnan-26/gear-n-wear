# Google Apps Script Email Integration — GEAR n WEAR

Send order confirmations, shipping updates, welcome emails, B2B enquiry alerts, and daily ops digests directly from your Gmail / Google Workspace account using Google Apps Script (no third-party SMTP or paid email API required).

## 1. Deploy the Google Apps Script Web App
1. Open [script.google.com](https://script.google.com) and click **New project**.
2. Replace the contents of `Code.gs` with the code from `google-apps-script/Code.gs` in this project.
3. *(Recommended)* Open **Project Settings** (gear icon) → **Script Properties** → **Add script property**:
   - **Property**: `EMAIL_SECRET`
   - **Value**: a random secret string (e.g. `gw_email_9f8a7b6c`)
4. Click **Deploy** → **New deployment**.
5. Select type **Web app**:
   - **Description**: `GEAR n WEAR Email Relay`
   - **Execute as**: `Me`
   - **Who has access**: `Anyone`
6. Click **Deploy**, authorize the Gmail/MailApp permissions when prompted, and copy the **Web app URL** (`https://script.google.com/macros/s/.../exec`).

## 2. Connect to Your Storefront
You can connect the Web App URL in **either** of two ways:

### Option A: Environment Variables (`.env`)
```env
GOOGLE_SCRIPT_URL=https://script.google.com/macros/s/YOUR_DEPLOYMENT_ID/exec
GOOGLE_SCRIPT_SECRET=gw_email_9f8a7b6c
```

### Option B: Admin Dashboard (No Restart Needed)
1. Log in to `/admin` → **Ops** tab.
2. Under **Google Apps Script Email**, paste your Web App URL and optional secret, then click **Save**.
3. Enter your email address and click **Send test email** to verify delivery.

## 3. Quota & Notes
- Free `@gmail.com` accounts allow **100 emails/day**; Google Workspace accounts allow **1,500 emails/day**.
- Whenever you edit `Code.gs`, create a **New deployment** (or manage deployments → New version) so the `/exec` URL serves the updated script.
