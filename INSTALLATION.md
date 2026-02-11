# 📲 Beet Anything - Installation Guide

Complete guide for installing, deploying, and using Beet Anything as a Progressive Web App.

**Version**: 1.2 Easy Layout  
**Last Updated**: February 2025

---

## 📋 Table of Contents

1. [Quick Start](#quick-start)
2. [Desktop Installation](#desktop-installation)
3. [Mobile Installation](#mobile-installation)
4. [Web Server Deployment](#web-server-deployment)
5. [Local Development](#local-development)
6. [Data Management](#data-management)
7. [Troubleshooting](#troubleshooting)

---

## 🚀 Quick Start

### Minimum Requirements

**Browser**:
- Chrome/Edge 90+ (recommended)
- Safari 14+
- Firefox 88+

**Device**:
- Desktop: Windows, macOS, Linux
- Mobile: iOS 14+, Android 8+
- Tablet: iPad, Android tablets

**Storage**:
- ~200 KB for app
- ~50 KB per garden (typical)

---

## 💻 Desktop Installation

### Method 1: Direct File Access (Easiest)

1. **Download the file**:
   ```bash
   # Clone repository
   git clone https://github.com/schatzl/beet-anything.git
   
   # Or download directly
   wget https://target23.de/beet-anything.html
   ```

2. **Open in browser**:
   - Double-click `beet-anything.html`
   - Or drag file into browser window
   - Works immediately, no installation needed

3. **Optional: Install as PWA**:
   - Click browser's install button (usually in address bar)
   - **Chrome**: Look for install icon ⊕ in address bar
   - **Edge**: Click "App available" prompt
   - **Safari**: File → Add to Dock

### Method 2: Bookmark for Quick Access

1. Open `beet-anything.html` in browser
2. Bookmark the page (Ctrl+D / Cmd+D)
3. Optionally: Add to bookmarks bar

### Method 3: System Integration

**Windows**:
```powershell
# Create desktop shortcut
$WshShell = New-Object -comObject WScript.Shell
$Shortcut = $WshShell.CreateShortcut("$Home\Desktop\Beet Anything.lnk")
$Shortcut.TargetPath = "chrome.exe"
$Shortcut.Arguments = "--app=file:///C:/path/to/beet-anything.html"
$Shortcut.Save()
```

**macOS**:
```bash
# Create app bundle (requires Automator or similar)
# Or use Safari's "Add to Dock" feature
```

**Linux**:
```bash
# Create .desktop file
cat > ~/.local/share/applications/beet-anything.desktop << EOF
[Desktop Entry]
Name=Beet Anything
Exec=google-chrome --app=file:///home/user/beet-anything.html
Icon=applications-other
Type=Application
EOF
```

---

## 📱 Mobile Installation

### iOS (iPhone/iPad)

1. **Open in Safari** (required for PWA on iOS):
   - Navigate to hosted URL, or
   - Email file to yourself and open in Safari

2. **Install to Home Screen**:
   - Tap Share button (box with arrow)
   - Scroll down and tap "Add to Home Screen"
   - Edit name if desired (default: "beet-anything")
   - Tap "Add"

3. **Launch**:
   - Find icon on home screen
   - Tap to launch full-screen app
   - Works offline after first load

**Note**: iOS requires Safari for PWA installation. Chrome/Firefox on iOS cannot install PWAs.

### Android

1. **Open in Chrome/Edge**:
   - Navigate to hosted URL, or
   - Use file manager to open `beet-anything.html`

2. **Install prompt**:
   - Browser shows "Add to Home screen" banner
   - Tap "Install" or "Add"
   - Or use menu: ⋮ → Add to Home screen

3. **Launch**:
   - Find icon in app drawer or home screen
   - Works like native app
   - Offline-capable after first load

---

## 🌐 Web Server Deployment

### Option 1: Static Hosting (Recommended)

**Supported platforms**:
- GitHub Pages
- Netlify
- Vercel
- AWS S3 + CloudFront
- Any static file host

**Steps**:

1. **Upload `beet-anything.html`** to hosting platform

2. **Configure HTTPS** (required for PWA):
   - Most platforms provide free SSL/TLS
   - GitHub Pages: Automatic
   - Netlify/Vercel: Automatic
   - Custom domain: Use Let's Encrypt

3. **Set headers** (optional, improves caching):
   ```nginx
   # nginx
   location /beet-anything.html {
       add_header Cache-Control "public, max-age=31536000";
       add_header Service-Worker-Allowed "/";
   }
   ```

4. **Test**:
   - Open URL in browser
   - Verify HTTPS (🔒 icon in address bar)
   - Check for install prompt

### Option 2: Apache Server

```apache
# .htaccess
<FilesMatch "beet-anything\.html">
    Header set Cache-Control "public, max-age=31536000"
    Header set Service-Worker-Allowed "/"
</FilesMatch>

# Enable HTTPS redirect
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

### Option 3: Node.js Express

```javascript
const express = require('express');
const app = express();

app.get('/beet-anything.html', (req, res) => {
    res.setHeader('Cache-Control', 'public, max-age=31536000');
    res.setHeader('Service-Worker-Allowed', '/');
    res.sendFile(__dirname + '/beet-anything.html');
});

app.listen(3000);
```

### CDN Deployment

**CloudFlare**:
1. Upload to origin server
2. Add site to CloudFlare
3. Enable "Always Use HTTPS"
4. Set cache rules for `.html` files

**AWS CloudFront**:
1. Upload to S3 bucket
2. Create CloudFront distribution
3. Configure SSL certificate
4. Set cache behaviors

---

## 🛠️ Local Development

### Live Server Setup

```bash
# Python 3
python3 -m http.server 8000

# Node.js (http-server)
npx http-server -p 8000

# PHP
php -S localhost:8000
```

Then open: `http://localhost:8000/beet-anything.html`

### VSCode Integration

Install "Live Server" extension:
1. Open `beet-anything.html` in VSCode
2. Right-click → "Open with Live Server"
3. Auto-reloads on file changes

### Testing PWA Features

**Chrome DevTools**:
1. Open DevTools (F12)
2. Application tab → Service Workers
3. Check "Update on reload"
4. Test offline: Network tab → Offline checkbox

**Lighthouse Audit**:
```bash
# Install Lighthouse CLI
npm install -g lighthouse

# Run PWA audit
lighthouse https://yoursite.com/beet-anything.html --view
```

---

## 💾 Data Management

### Backup

**Manual Export**:
1. Open app
2. Settings (gear icon) → Export Data
3. Save JSON file to safe location
4. Recommended: Weekly backups

**Automated Backup** (advanced):
```javascript
// Browser console
const data = localStorage.getItem('beetAnything_garden');
const blob = new Blob([data], {type: 'application/json'});
const url = URL.createObjectURL(blob);
const a = document.createElement('a');
a.href = url;
a.download = `garden-backup-${new Date().toISOString()}.json`;
a.click();
```

### Import

1. Settings → Import Data
2. Select previously exported JSON file
3. Confirm overwrite (or merge, if supported)
4. Data restored immediately

### Migration Between Devices

**Method 1: Export/Import**:
1. Device A: Export data
2. Transfer JSON file (email, cloud, USB)
3. Device B: Import data

**Method 2: Cloud Sync** (requires custom setup):
- Use browser sync (Chrome Sync, iCloud)
- localStorage syncs automatically
- Or implement custom cloud backend

### Clear Data

**Reset app**:
1. Settings → Clear All Data
2. Confirm deletion
3. All beds, plantings, wishlist removed
4. Cannot be undone (unless backed up)

**Browser storage**:
- Chrome: DevTools → Application → Storage → Clear
- Firefox: DevTools → Storage → localStorage → Delete
- Safari: Develop → Storage → localStorage

---

## 🔧 Troubleshooting

### App Won't Load

**Symptom**: Blank page or error message

**Solutions**:
1. Check browser version (need Chrome 90+, Safari 14+, Firefox 88+)
2. Disable browser extensions (ad blockers may interfere)
3. Clear cache and hard reload (Ctrl+Shift+R / Cmd+Shift+R)
4. Try different browser
5. Check console for errors (F12 → Console)

### Can't Install as PWA

**Symptom**: No install button/prompt

**Solutions**:
1. **HTTPS required**: Must be served over HTTPS (or localhost)
2. **Desktop Chrome**: Look for ⊕ icon in address bar
3. **Desktop Edge**: Check for "App available" notification
4. **Mobile Safari**: Use Share → Add to Home Screen
5. **Android Chrome**: Check for banner or ⋮ menu → Add to Home screen

### Data Not Saving

**Symptom**: Changes lost after refresh

**Solutions**:
1. Check localStorage available: Console → `typeof localStorage`
2. Check quota not exceeded: Console → `navigator.storage.estimate()`
3. Disable private/incognito mode (localStorage disabled)
4. Allow cookies/storage in browser settings
5. Check if browser storage cleared on exit

### Offline Mode Not Working

**Symptom**: Requires internet after installation

**Solutions**:
1. Load page once with internet (caches assets)
2. Check Service Worker: DevTools → Application → Service Workers
3. Verify HTTPS (Service Workers require secure context)
4. Clear and re-register: DevTools → Application → Service Workers → Unregister

### Mobile Layout Issues

**Symptom**: Content doesn't fit screen

**Solutions**:
1. Portrait orientation recommended for phones
2. Landscape works better on tablets
3. Zoom reset: Settings → Reset zoom
4. Check browser zoom (should be 100%)
5. Update browser to latest version

### Performance Issues

**Symptom**: Slow or laggy interface

**Solutions**:
1. Export data, clear app, re-import (cleans corrupted data)
2. Reduce number of beds (50+ may slow down)
3. Close other browser tabs
4. Clear browser cache
5. Restart browser/device

### Translation Not Working

**Symptom**: English shows instead of German/Italian

**Solutions**:
1. Click language button (EN/DE/IT) explicitly
2. Clear cache and reload
3. Check localStorage: Console → `localStorage.getItem('beetAnything_language')`
4. Set manually: Console → `localStorage.setItem('beetAnything_language', 'de')`

---

## 📊 System Status Check

Run in browser console to verify setup:

```javascript
// Check localStorage
console.log('localStorage available:', typeof localStorage !== 'undefined');
console.log('Current language:', localStorage.getItem('beetAnything_language'));
console.log('Garden data size:', localStorage.getItem('beetAnything_garden')?.length || 0);

// Check storage quota
navigator.storage.estimate().then(estimate => {
    console.log('Storage used:', estimate.usage);
    console.log('Storage quota:', estimate.quota);
    console.log('Percentage:', (estimate.usage / estimate.quota * 100).toFixed(2) + '%');
});

// Check PWA features
console.log('Service Worker supported:', 'serviceWorker' in navigator);
console.log('Standalone mode:', window.matchMedia('(display-mode: standalone)').matches);
```

---

## 🌍 Hosting Examples

### GitHub Pages

```bash
# Push to repository
git add beet-anything.html
git commit -m "Update app"
git push origin main

# Enable Pages: Settings → Pages → Source: main branch
# URL: https://username.github.io/beet-anything/beet-anything.html
```

### Netlify

```bash
# Drop file in Netlify dashboard, or:
netlify deploy --prod --dir=. --site=beet-anything
```

### Vercel

```bash
# Create vercel.json
{
  "routes": [
    { "src": "/", "dest": "/beet-anything.html" }
  ]
}

# Deploy
vercel --prod
```

---

## 🔒 Security Notes

**Data Privacy**:
- All data stored locally (localStorage)
- No server communication
- No telemetry or analytics
- Export/import is plain JSON (human-readable)

**Browser Permissions**:
- No geolocation
- No camera/microphone
- No notifications (optional)
- Only localStorage access needed

**HTTPS Requirement**:
- Required for Service Worker
- Required for some PWA features
- Use Let's Encrypt for free SSL

---

## 📞 Support

**Issues**: https://github.com/schatzl/beet-anything/issues  
**Live Demo**: https://target23.de/beet-anything.html  
**Documentation**: See README.md and SPECIFICATION.md

---

**Happy Gardening! 🌱**
