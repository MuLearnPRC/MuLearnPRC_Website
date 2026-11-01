# MuLearn PRC Official Website

> **Official web portal for the μLearn chapter at Providence College of Engineering (PRC).**  
> Designed and built for seamless community presentation, dynamic member recognition, automated live leaderboard rankings, and scalable event showcases.

---

## 📋 Manager's Executive Summary

Welcome to the **MuLearn PRC Website** repository! If you are a chapter lead, technical lead, or website manager taking over maintenance, this document is your complete handbook. 

The website is engineered with a **zero-overhead, serverless static architecture**:
- **No Build Tools Required:** Pure vanilla HTML5, CSS3, and ES6+ JavaScript. No node build pipelines, bundlers, Webpack, or Vite to configure or break.
- **Client-Side Live Data:** Dynamic sections (Executive Committee, Karma Leaderboard, Events, and Gallery) fetch directly from **Google Sheets via CSV export**. You can update site content in seconds by editing spreadsheets without touching any code.
- **Zero-Crop Aspect Ratio Engine:** Adaptive bento framing displays event posters in their native proportions without text truncation or awkward letterbox bars.
- **Autonomous Dual-Theme Background:** Smooth light/dark video canvas that ping-pongs seamlessly across all viewports.

---

## 🗂️ Repository Structure

```
MuLearnPRC_Website/
├── index.html              # Main HTML entry point containing all section structures
├── style.css               # Unified styling (design tokens, layout, themes, bento grid)
├── app.js                  # Core controller: theme toggling, mobile drawer, Events bento engine
├── execom.js               # Fetches & renders Executive Committee member cards from CSV
├── leaderboard.js          # Fetches, sorts & animates the Karma Leaderboard from CSV
├── gallery.js              # Fetches & renders the 3-row horizontal infinite gallery from CSV
│
├── events_urls.json        # Offline backup of Event poster URLs
├── gallery_urls.json       # Offline backup of Gallery showcase URLs
│
├── assets/                 # Static media assets
│   ├── logo.png            # Chapter μLearn brand logo (navbar)
│   ├── hero_team.png       # Hero section featured team banner
│   ├── Lightmode/          # Light theme assets
│   │   └── Lightmode_smooth.mp4
│   └── Darkmode/           # Dark theme assets
│       └── Darkmode_smooth.mp4
│
├── frames/                 # Source video frame stills
├── gsap.min.js             # GSAP animation core library
├── ScrollTrigger.min.js     # GSAP ScrollTrigger plugin
├── papaparse.min.js        # High-performance client-side CSV parser
└── README.md               # Manager's handbook & documentation
```

---

## 🛠️ Technology Stack

| Layer | Technology | Role & Purpose |
| :--- | :--- | :--- |
| **Markup** | HTML5 (Semantic) | Accessible sections, modular containers, responsive meta |
| **Styling** | CSS3 & Custom Properties | Glassmorphism, animations, responsive flex/grid layouts |
| **Logic** | Vanilla JavaScript (ES6+) | Direct DOM manipulation, async CSV fetching, dynamic rendering |
| **Data Parsing** | PapaParse (`papaparse.min.js`) | Fast browser-side CSV streaming and record transformation |
| **Animation** | GSAP & ScrollTrigger | Scroll-linked triggers, smooth video scrubbing, count-ups |
| **Data Storage** | Google Sheets | Live, no-code CMS for leads and chapter managers |
| **Asset Delivery**| Google Drive / Google CDN | Direct image and poster hosting (`lh3.googleusercontent.com`) |

---

## 📊 Live Google Sheets Data Integration (Manager's Guide)

The website uses Google Sheets as a headless CMS. All changes made in the linked Google Sheets reflect on the website immediately upon page refresh.

### Connected Google Sheets Overview

| Section | Script File | Active Google Sheet ID | Purpose |
| :--- | :--- | :--- | :--- |
| **Execom Members** | `execom.js` | `15HUWRhfpYHVxINOAzEvz2eF0t3o5EHgj` | Leadership team cards and roles |
| **Karma Leaderboard** | `leaderboard.js` | `1aZSfgWH9AKmDjOsUasiYE29tMsAC8jR1` | Student karma rankings, points, and badges |
| **Events Marquee** | `app.js` | `1k9kTBNXbByQWnK34ChZGNjt6FGqRC5smw4g7mYnZXl0` | 10+ photo adaptive bento marquee |
| **Dynamic Gallery** | `gallery.js` | `1bseK0dgDdbsBYoHn4nx28zqTcbMRFcDA` | 3-row infinite showcase photo stream |

---

### Sheet Schemas & Formatting Rules

#### 1. Execom Sheet (`execom.js`)
- **Configured at:** `execom.js` (Line 2: `CSV_URL`)
- **Expected Columns (Case-Sensitive):**
  ```csv
  Team,Role,Name,Photo Link
  Campus Leadership,Campus Lead,Georgy Benoy,https://drive.google.com/uc?id=...
  Campus Leadership,Campus Co-Lead,Jesty Jose,https://drive.google.com/uc?id=...
  ```
- **Photo Link:** Direct Google Drive link. The script automatically converts standard sharing links into high-speed Google User Content endpoints (`lh3.googleusercontent.com/d/{ID}`).
- **Initials Fallback:** If a photo is missing or fails to load, the card automatically falls back to an elegant circular avatar with the member's initials.

#### 2. Karma Leaderboard Sheet (`leaderboard.js`)
- **Configured at:** `leaderboard.js` (Line 2: `CSV_URL`)
- **Expected Columns (Case-Sensitive):**
  ```csv
  full_name,muid,karma,rank,level,join_date,last_karma_gained,graduation_year,department,is_alumni,ig_count,lc_count
  ```
- **Top 3 Highlighting:** The leaderboard script automatically detects ranks 1, 2, and 3, awarding gold, silver, and bronze medal badges (🥇🥈🥉).
- **Point Animations:** When the user scrolls into the leaderboard, the karma point numbers dynamically animate upwards.

#### 3. Events Marquee Sheet (`app.js`)
- **Configured at:** `app.js` (Line 242: `EVENTS_CSV_URL`)
- **Expected Structure:** Single column of Google Drive image sharing URLs in Column A:
  ```csv
  https://drive.google.com/file/d/{FILE_ID}/view?usp=sharing
  https://drive.google.com/file/d/{FILE_ID}/view?usp=sharing
  ```
- **Aspect Ratio Handling:** The Events engine automatically detects image ratios and paces them into a balanced bento rhythm (Full-Height Hero ↔ 2-Stacked Paired Column).
- **Scalability:** Supports any number of photos $N$ (10, 15, 30, 50+).

#### 4. Gallery Sheet (`gallery.js`)
- **Configured at:** `gallery.js` (Line 3: `csvUrl`)
- **Expected Structure:** Single column of Google Drive image sharing links:
  ```csv
  https://drive.google.com/file/d/{FILE_ID}/view?usp=sharing
  https://drive.google.com/file/d/{FILE_ID}/view?usp=sharing
  ```
- **Triple-Track Scrolling:** Images are distributed across three horizontal tracks scrolling in alternating directions at varying speeds.

---

### ⚠️ Google Drive Link Optimization

Google Drive links formatted as `https://drive.google.com/uc?export=view&id=...` often trigger 302 redirects and cross-site tracking blocks in modern browsers.

All scripts in this codebase automatically transform URLs to:
```
https://lh3.googleusercontent.com/d/{FILE_ID}
```
This directly accesses Google's CDN edge cache, guaranteeing instant image loading, zero cookie warnings, and zero tracking prevention blocks.

> **Crucial Setting:** Make sure any Google Sheet or Google Drive folder/file used for the site is set to **"Anyone with the link can view"** in Google Drive sharing permissions.

---

## 💻 Local Development & Testing

Since this project requires no compiler or package manager, getting started takes less than 30 seconds.

### 1. Clone the Repository
```bash
git clone https://github.com/MuLearnPRC/MuLearnPRC_Website.git
cd MuLearnPRC_Website
```

### 2. Run a Local Server
Because modern browsers restrict cross-origin `fetch()` and `PapaParse` requests from the `file://` protocol, you should view the site through a local HTTP server:

**Option A (Python 3):**
```bash
python -m http.server 8000
# Open http://localhost:8000 in your browser
```

**Option B (Node.js npx):**
```bash
npx serve .
# Or: npx live-server
```

**Option C (VS Code Extension):**
Install the **Live Server** extension in VS Code, right-click `index.html`, and select **"Open with Live Server"**.

---

## 🎨 Design System & Customization

### 1. Theme Configuration
The site supports both light and dark themes via CSS custom properties on `:root` and `[data-theme="dark"]` in `style.css`:
```css
:root {
  --color-primary: #7c3aed;        /* Vibrant Purple */
  --color-primary-hover: #9333ea;  /* Hover state */
  --color-bg-card: rgba(255, 255, 255, 0.7);
  --border-radius-md: 16px;
}

[data-theme="dark"] {
  --color-bg-card: rgba(255, 255, 255, 0.05);
  --color-border: rgba(124, 58, 237, 0.25);
}
```
The selected theme is saved in `localStorage('theme')` and crossfades the background canvas video.

### 2. Header Navigation
- **Fixed Position:** The navigation bar is set to `position: fixed` with frosted blur (`backdrop-filter: blur(12px)`), remaining visible across full-page scrolling.
- **Main Offset:** `.main-content` includes top padding (`padding-top: 76px`) to prevent content from hiding beneath the header.

### 3. Cache Management
When updating JavaScript or CSS files, increment the cache-buster query parameter in `index.html` (e.g. `style.css?v=8`, `app.js?v=7`) so returning users immediately receive the new code without caching issues.

---

## 🔍 Troubleshooting Guide for Managers

### 1. Member cards or Leaderboard show "Loading..." or fail to appear
- **Cause:** Google Sheet permission is private or column names were modified.
- **Fix:** Open the sheet → Click **Share** → Ensure General Access is **"Anyone with the link can view"**. Verify column names match the schema listed above.

### 2. An image fails to load or shows a broken placeholder icon
- **Cause:** The Google Drive image is either not shared publicly, or the file ID is invalid.
- **Fix:** In Google Drive, ensure the file or folder is set to public view. The site automatically displays a fallback avatar with initials or a camera placeholder so the layout never breaks.

### 3. Background video is static or black
- **Cause:** Browser autoplay policy blocked video playback, or video files were renamed.
- **Fix:** Ensure `video.muted = true` (autoplay requires muted state). Verify `assets/Lightmode/Lightmode_smooth.mp4` and `assets/Darkmode/Darkmode_smooth.mp4` are present.

---

## 🤝 Contribution & Governance

- **Repository:** [`MuLearnPRC/MuLearnPRC_Website`](https://github.com/MuLearnPRC/MuLearnPRC_Website)
- **Primary Branch:** `main`
- **Author:** **RYuK006**
- **Community:** [μLearn](https://mulearn.org) • Providence College of Engineering (PRC)

### Recommended Workflow for Updates
1. Pull latest changes: `git pull origin main`
2. Test changes locally via a local HTTP server
3. Commit with concise, descriptive commit messages
4. Push directly to `main`
