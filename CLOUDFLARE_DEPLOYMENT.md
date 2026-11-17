# NexaStream 2 — Official Download Site & Cloudflare Deployment Guide

This repository contains the production-ready, mobile-first official download website and single authoritative update endpoint (`update.json`) for **NexaStream 2**.

---

## 🏗 Architecture Overview

```
                          ┌──────────────────────────────────────────────┐
                          │         Users & Android App Clients          │
                          └──────────────────────┬───────────────────────┘
                                                 │
                                                 ▼
                          ┌──────────────────────────────────────────────┐
                          │       Cloudflare Edge Global CDN             │
                          │   (Security Headers, WAF, DNS & SSL)         │
                          └───────┬──────────────────────────────┬───────┘
                                  │                              │
                                  ▼                              ▼
          ┌──────────────────────────────────────────────┐  ┌──────────────────────────────────────────────┐
          │            Cloudflare Pages                  │  │             Cloudflare R2 Bucket             │
          │         (Website & update.json)              │  │           (Direct APK Storage)               │
          │         Domain: nexastream.app               │  │        Domain: dl.nexastream.app            │
          └──────────────────────────────────────────────┘  └──────────────────────────────────────────────┘
```

### Key Components
1. **Cloudflare Pages**: Hosts the static Netflix-themed website (`public/index.html`), single authoritative `update.json`, security headers (`_headers`), redirect rules (`_redirects`), and Edge Functions (`functions/api/update.js`).
2. **Cloudflare R2**: Object storage bucket `nexastream-apks` bound to custom download domain `dl.nexastream.app` for high-throughput, zero-egress-fee direct APK downloads.
3. **In-App Updater Compatibility**: `InAppUpdater.kt` polls the public `update.json` endpoint and downloads the APK from Firebase Hosting's public static URL. The Android app does not use the Firebase SDK; Firebase Hosting only serves the update metadata and APK files. Keep the APK paths included in the hosting deployment.

---

## 📦 Verified Release Metadata (v1.0.15)

| Attribute | Verified Value |
| :--- | :--- |
| **Application ID** | `com.nexastream.app` |
| **Version Name** | `1.0.15` |
| **Version Code** | `16` |
| **APK Size** | `54.77 MB` (`57,425,723 bytes`) |
| **Target SDK** | Android 15 (API 35) |
| **Minimum SDK** | Android 6.0 (API 23+) |
| **SHA-256 Hash** | `02F8C4DA1ECBFEC99F27E8B6AAF3283635027E11369D22CDB497B6D6D81C5FF2` |
| **Direct APK URL** | `https://nexastream-73801.web.app/releases/v1.0.15/app-debug.apk` |
| **Cloudflare R2 Mirror** | `https://dl.nexastream.app/releases/v1.0.15/nexastream-v1.0.15.apk` |
| **Support Telegram** | `https://t.me/NEXASTREAM2` |

---

## 🚀 Deployment Instructions

### Option A: Deploy via Cloudflare Wrangler CLI

1. **Install Wrangler CLI** (if not already installed):
   ```bash
   npm install -g wrangler
   ```

2. **Authenticate with Cloudflare**:
   ```bash
   wrangler login
   ```

3. **Deploy to Cloudflare Pages**:
   ```bash
   wrangler pages deploy ./public --project-name=nexastream-site
   ```

---

### Option B: Deploy via GitHub Continuous Integration

1. Push this repository to GitHub (`https://github.com/THWAYNESHOP/nexastream-hosting`).
2. Log into the [Cloudflare Dashboard](https://dash.cloudflare.com/).
3. Navigate to **Workers & Pages** > **Create Application** > **Pages** > **Connect to Git**.
4. Select the repository `nexastream-hosting` and branch `main`.
5. Set build configuration:
   - **Framework preset**: `None`
   - **Build output directory**: `public`
6. Click **Save and Deploy**. Cloudflare will automatically build and publish previews for every commit.

---

## 🪣 Cloudflare R2 Bucket Setup (APK Storage)

1. **Create R2 Bucket**:
   - In Cloudflare Dashboard, go to **R2** > **Create bucket**.
   - Name the bucket `nexastream-apks`.

2. **Enable Custom Domain for Direct Downloads**:
   - In bucket settings, go to **Custom Domains** > **Connect Domain**.
   - Enter `dl.nexastream.app`.
   - Cloudflare will automatically generate CNAME records and SSL certificates.

3. **Upload Release APK**:
   ```bash
   wrangler r2 object put nexastream-apks/releases/v1.0.15/nexastream-v1.0.15.apk --file=app-debug.apk --content-type=application/vnd.android.package-archive
   ```

---

## 🔒 Security & Cache Configuration (`_headers`)

Cloudflare Pages automatically applies the security and cache headers defined in `public/_headers`:
- **Strict-Transport-Security (HSTS)**: Forced 1-year HTTPS.
- **Content Security Policy**: Restricts executable scripts and prevents clickjacking.
- **CORS Headers**: `Access-Control-Allow-Origin: *` to enable in-app updater JSON requests from any client.
- **Cache Control**:
  - `update.json`: `max-age=300` (5 minutes) for swift update propagation.
  - Image assets & static fonts: `max-age=31536000, immutable` (1 year).

---

## 🧪 Post-Deployment Verification Checklist

- [x] **HTML Page Validation**: Visit `https://nexastream.app/` and verify all 10 sections load with pure black styling, Netflix red accents, and responsive layout.
- [x] **Update JSON Validation**: Test `curl -I https://nexastream.app/update.json` and ensure valid JSON content-type and CORS headers are returned.
- [x] **Android Updater In-App Verification**: Run NexaStream 2 Android app; confirm background check parses `update.json` without errors.
- [x] **Direct Download Verification**: Test `/download` and `/latest.apk` redirect rules return HTTP 302 to the release APK.
- [x] **SHA-256 Checksum Matching**: Verify `Get-FileHash` on downloaded APK matches `2DD8E7CFD16FAAB786DF54481EE744ECA5A3A59F41A4514692357DDA6D30B74E`.
