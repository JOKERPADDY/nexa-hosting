# NexaStream Hosting & Official Download Portal

Official web hosting repository for **NexaStream 2**, featuring a Netflix-inspired streaming portal, OTA update configuration (`update.json`), APK release management, and multi-platform deployment setup (Firebase Hosting & Cloudflare Pages / R2).

Repository: [https://github.com/JOKERPADDY/nexa-hosting.git](https://github.com/JOKERPADDY/nexa-hosting.git)

---

## 🌟 Features

- **Cinematic Landing Page**: Pure black Netflix-themed interface highlighting NexaStream 2 features (Live TV, VOD, 4K streaming, remote control support, zero ads).
- **In-App OTA Updates (`update.json`)**: Authoritative version checking and automatic background update support for Android TV and mobile clients.
- **Direct APK Downloads**: Seamless redirects and static hosting for direct app installation and sideloading.
- **Edge Deployment Ready**: Configured for Cloudflare Pages, Cloudflare Workers, and Firebase Hosting.

---

## 📂 Project Structure

```text
nexastream-hosting/
├── public/
│   ├── index.html            # Official landing page & download portal
│   ├── _headers              # CORS, cache control, and security headers
│   ├── _redirects            # URL shorteners (/download, /latest.apk)
│   ├── update.json           # In-app update metadata (v1.0.15)
│   ├── assets/               # Banners, logos, and UI screenshots
│   └── releases/             # APK binaries for OTA updates (v1.0.9 - v1.0.15)
├── functions/
│   └── api/                  # Edge functions (update API endpoint)
├── .github/workflows/        # CI/CD deployment pipelines
├── CLOUDFLARE_DEPLOYMENT.md  # Detailed Cloudflare Pages & R2 guide
├── firebase.json             # Firebase Hosting configuration
└── README.md                 # Project documentation
```

---

## 📦 Current Release Metadata (v1.0.15)

- **Version Name**: `1.0.15`
- **Version Code**: `16`
- **Min SDK**: Android 6.0 (API 23+)
- **Target SDK**: Android 15 (API 35)
- **Direct APK URL**: `https://nexastream-73801.web.app/releases/v1.0.15/app-debug.apk`

---

## 🚀 Deployment

### Firebase Hosting
```bash
firebase deploy --only hosting
```

### Cloudflare Pages
```bash
npx wrangler pages deploy ./public --project-name=nexastream-hosting
```

---

## 📄 License

Proprietary © NexaStream 2. All rights reserved.
