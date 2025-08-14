# Bullish Sweeps ‚Äî Quick Launch Guide (GitHub Pages + Google Sheets)

This stack gives you a pretty front-end on **BullishWeeps.com** and a low-friction backend you control.

## What you need
- Google account (for Sheets + Apps Script)
- GitHub account (for GitHub Pages)
- Your domain **BullishWeeps.com** DNS access

---

## Step 1 ‚Äî Create the Google Sheet
1. Create a blank Google Sheet named **Bullish Sweeps**.
2. Add 2 tabs:
   - **Form Responses 1** (exact name) with this header row in row 1:
     ```
     Timestamp,Entry Type,Entry Date,Wallet Address,Email,Full Name,Tx Hash,Referred Wallet,Evidence Link
     ```
   - **Entries** (exact name) with this header row in row 1:
     ```
     timestamp_utc,entry_date,entry_type,email,full_name,wallet_address,tx_hash_optional,referred_wallet_optional,evidence_link_optional,notes_admin,entries_awarded,tokens_awarded,is_verified,verified_by,verified_at_utc,dedup_key
     ```

> Note: You can import historical claims using the CSV template I provided earlier, or paste rows directly into **Entries** (keep the columns in order).

---

## Step 2 ‚Äî Add the Apps Script backend
1. In the Google Sheet: **Extensions ‚Üí Apps Script**.
2. Delete any default code and paste the contents of **apps_script.gs** from this repo.
3. Click the **Save** icon.
4. Deploy it as a Web App:
   - **Deploy ‚Üí New deployment**
   - Select **Web app**
   - **Execute as:** Me
   - **Who has access:** Anyone
   - Click **Deploy** and copy the **Web app URL** (you‚Äôll paste it into `index.html`).

Optional (but nice): **Triggers**
- Not required with this setup‚Äîthe `doPost` writes to `Entries` directly and applies awards.
- You can use the onOpen menu already provided to pick a winner.

---

## Step 3 ‚Äî Prepare the front-end (GitHub)
1. Create a new GitHub repo (public is fine): `bullishweeps-site`.
2. Upload **index.html** from this repo.
3. Edit `index.html` and replace `REPLACE_WITH_YOUR_WEB_APP_URL` with the Web App URL from Step 2.
4. Commit to the `main` (or `master`) branch.

---

## Step 4 ‚Äî Enable GitHub Pages
1. In the GitHub repo: **Settings ‚Üí Pages**.
2. **Source:** Deploy from a branch
3. **Branch:** `main` / `/ (root)`
4. Save. GitHub will publish at `https://YOUR_USERNAME.github.io/bullishweeps-site/`.

---

## Step 5 ‚Äî Point your custom domain
1. In **Settings ‚Üí Pages**, set **Custom domain** to `bullishweeps.com` (or a subdomain like `enter.bullishweeps.com`).
2. GitHub will tell you which DNS record to add. Usually:
   - **CNAME** for `www.bullishweeps.com` ‚Üí `YOUR_USERNAME.github.io`
   - For apex, use an ALIAS/ANAME at your DNS provider or redirect apex ‚Üí `www`.
3. Wait for DNS to propagate, then enable **Enforce HTTPS**.

> If your DNS provider doesn‚Äôt support apex CNAME, use `www.bullishweeps.com` as the custom domain and set an apex ‚Üí `www` redirect at your registrar.

---

## Step 6 ‚Äî Admin workflow (verification & drawing)
- Open the Google Sheet ‚Üí **Entries** tab.
- Use filters to review claims. Mark verified rows via the Apps Script function `verifyRows([2,3,4], 'Nicholas')` (Apps Script ‚Üí Run).
- When ready, run `pickWinner()` (from the custom **Bullish Sweeps** menu or Apps Script editor). It draws a weighted winner by `entries_awarded` where `is_verified=TRUE`.

### Suggested verification
- **Daily submission / streak:** check `tx_hash_optional` on Snowtrace.
- **Unlock 18 docks:** confirm via screenshot or contract read; then verify once.
- **Referral:** verify referee wallet has at least 1 unlock and 1 daily submission.

---

## Step 7 ‚Äî Backfilling entries (since Aug 1, 2025)
- Share the site and tell users to pick earlier dates for backfill (calendar allows Aug 1+).
- Or bulk-import a CSV into **Entries** (don‚Äôt change column order).

---

## Step 8 ‚Äî Anti-fraud & guardrails
- Wallet regex enforced client-side; server also checks format and date window.
- Dedup key: `(entry_date, entry_type, wallet)` prevents double-claims for the same day/type.
- Hidden honeypot field to deter simple bots.
- You can add email confirmation later via Mailchimp or Apps Script Gmail if desired.

---

## Step 9 ‚Äî The Friday runbook
1. In **Entries**, set a filter for `entry_date` between Aug 1‚Äìthe drawing date and `is_verified = TRUE`.
2. If needed, run `verifyRows([...])` to approve legitimate rows in bulk.
3. Run `pickWinner()`; announce name/email/wallet and keep the screenshot/toast for audit.
4. Export a CSV snapshot for records.

<img width="462" height="642" alt="image" src="https://github.com/user-attachments/assets/d383a503-d532-4db6-bea3-30678b676f73" />
