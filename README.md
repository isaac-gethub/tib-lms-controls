# TIB Systems Inc. — SAP Controls & Compliance Training Programme
## Deployment README — `KABO-TRN-CTL-WPB-001`

**Version:** 1.0  
**Prepared by:** TIB Systems Inc.  
**Contact:** academy@tib-systems.com | tib-systems.com

---

## 1. What This Is

A fully self-contained, browser-based LMS for the SAP Controls & Compliance Training Programme. Ten modules (B1–B10), no server required, no database, no backend. All state is stored in the browser's `localStorage`.

**Technology stack:**
- Plain HTML5 / CSS3 / Vanilla JavaScript
- Claude Sonnet API (for in-module AI Tutor — requires internet connection and API key)
- `localStorage` for progress persistence (browser-local, no server)
- No npm, no build step, no dependencies to install

---

## 2. Minimum Requirements

| Requirement | Detail |
|---|---|
| Browser | Chrome 90+, Edge 90+, Firefox 88+, Safari 14+. **No IE support.** |
| Internet | Required only for the AI Tutor feature. All other content works offline. |
| Screen | 1024px minimum width recommended. Responsive down to ~640px. |
| JavaScript | Must be enabled. |
| localStorage | Must not be blocked (private/incognito mode may limit persistence). |

---

## 3. Deployment Options

### Option A — Local File (Simplest)
No server needed. Double-click `index.html` to launch.

```
1. Copy the entire LMS folder to your computer or USB drive.
2. Double-click index.html.
3. The programme opens in your default browser.
```

> **Note:** The AI Tutor will not work in some browsers (e.g. Chrome) when opened as a local file due to CORS restrictions. Use Option B or C for full AI Tutor functionality.

---

### Option B — Static Web Host (Recommended for teams)

Upload the entire folder contents to any static hosting provider. No server-side processing required.

**Recommended providers (all support static HTML):**

| Provider | Free Tier | Custom Domain | Notes |
|---|---|---|---|
| **Vercel** | Yes | Yes | `vercel deploy` or drag-and-drop |
| **Netlify** | Yes | Yes | Drag folder to netlify.com/drop |
| **GitHub Pages** | Yes | Yes | Push to `gh-pages` branch |
| **AWS S3 + CloudFront** | Paid | Yes | Enterprise option |
| **Azure Static Web Apps** | Free tier | Yes | Microsoft ecosystem |

**Vercel deployment (fastest):**
```bash
# Install Vercel CLI (once)
npm install -g vercel

# From the LMS folder
vercel deploy

# Follow prompts. Your LMS will be live at a *.vercel.app URL.
```

**Netlify drag-and-drop:**
```
1. Go to netlify.com/drop
2. Drag the entire LMS folder onto the page.
3. Your LMS is live immediately at a *.netlify.app URL.
```

---

### Option C — Local Web Server (for development/testing)

Run a local server to enable the AI Tutor in development:

```bash
# Python (built-in, no install needed)
cd /path/to/lms-folder
python3 -m http.server 8080
# Open http://localhost:8080 in your browser

# Node.js (if installed)
npx serve .
# Open the URL it shows
```

---

## 4. File Structure

All files must remain in the **same folder**. Do not create subfolders for HTML files.

```
lms/
├── index.html                          ← Landing page (start here)
├── b1.html                             ← Module B1: Risk Types
├── module-shell.html                   ← Module B2: Control Types
├── b3.html                             ← Module B3: Control Nature
├── b4.html                             ← Module B4: Why Controls Matter
├── b5.html                             ← Module B5: Engagement Types
├── b6.html                             ← Module B6: S/4HANA Lifecycle
├── b7.html                             ← Module B7: Reading FD Documents
├── b8.html                             ← Module B8: Testing Phase
├── b9.html                             ← Module B9: AI in Controls
├── b10.html                            ← Module B10: Practitioner Capstone
├── practitioner-toolkit.pdf           ← Toolkit download (linked from b10 + index)
└── KABO-TRN-CTL-TLK-001-Practitioner-Toolkit.pdf  ← Canonical toolkit filename
```

> **Critical:** If you rename any HTML file, update the corresponding `onclick` and `href` references across all other files. The easiest approach is a global find-and-replace in a code editor.

---

## 5. AI Tutor Configuration

Each module's AI Tutor connects directly to the Anthropic Claude API from the browser.

**Current API key location:** Hardcoded in each module's `<script>` block as `CLAUDE_API_KEY`.

```javascript
// In every module file — search for this line:
const CLAUDE_API_KEY = 'sk-ant-api03-...';
```

**To update the API key:**
```bash
# Replace across all module files at once (macOS/Linux):
find . -name "*.html" -exec sed -i '' \
  "s/sk-ant-api03-OLD_KEY/sk-ant-api03-NEW_KEY/g" {} \;

# Windows (PowerShell):
Get-ChildItem *.html | ForEach-Object {
  (Get-Content $_) -replace 'OLD_KEY', 'NEW_KEY' | Set-Content $_
}
```

**Security note:** The API key is visible in browser DevTools. For production deployments with many concurrent users, proxy the API call through a serverless function (Vercel Edge Function, Netlify Function, etc.) to keep the key server-side.

**API model:** All tutors use `claude-sonnet-4-20250514`. Update in each module's `SYSTEM_PROMPT` section if switching models.

---

## 6. Progress and localStorage

All learner progress is stored in `localStorage` under these keys:

| Key | Type | Set by |
|---|---|---|
| `tib_learner_name` | String | index.html name input |
| `tib_module_b1_complete` | `'true'` | b1.html on quiz pass |
| `tib_module_b2_complete` | `'true'` | module-shell.html on quiz pass |
| `tib_module_b3_complete` | `'true'` | b3.html on quiz pass |
| `tib_module_b4_complete` | `'true'` | b4.html on quiz pass |
| `tib_module_b5_complete` | `'true'` | b5.html on quiz pass |
| `tib_module_b6_complete` | `'true'` | b6.html on quiz pass |
| `tib_module_b7_complete` | `'true'` | b7.html on quiz pass |
| `tib_module_b8_complete` | `'true'` | b8.html on quiz pass |
| `tib_module_b9_complete` | `'true'` | b9.html on quiz pass |
| `tib_module_b10_complete` | `'true'` | b10.html on quiz pass |

**Important limitations:**
- Progress is **per browser, per device**. A learner who switches browser or device starts fresh.
- Private/incognito mode: progress is not saved between sessions.
- Clearing browser data clears all progress. The index.html landing page has a "Reset Progress" button for deliberate resets.
- There is no server-side persistence. If you need cross-device progress, a backend sync layer (Supabase, Firebase, etc.) would need to be added.

---

## 7. Quiz Pass Thresholds

| Module | Questions | Pass Threshold |
|---|---|---|
| B1 | 5 | 60% (3/5) |
| B2 | 5 | 60% (3/5) |
| B3 | 5 | 60% (3/5) |
| B4 | 5 | 60% (3/5) |
| B5 | 5 | 60% (3/5) |
| B6 | 5 | 60% (3/5) |
| B7 | 5 | 60% (3/5) |
| B8 | 4 | 50% (2/4) |
| B9 | 5 | 60% (3/5) |
| B10 | 5 | 60% (3/5) |

To change a threshold, find `pct>=60` (or `pct>=50` for B8) in the relevant module's `showScoreScreen()` function.

---

## 8. Branding Customisation

The colour scheme is defined in `design-system.css` (if present) or via CSS variables in each file's `<style>` block. Core brand colours:

```css
--navy:  #1F3864   /* Primary — headers, sidebar, dark surfaces */
--teal:  #1F7A8C   /* Accent — interactive elements, progress, links */
--amber: #C65911   /* Highlight — badges, warnings, exercise labels */
```

The TIB Systems logo mark ("T") and brand name appear in every module header. To update branding:
- Search for `TIB Systems` across all HTML files
- Replace `lms-header__logo-mark` text and `lms-header__brand-name` text

---

## 9. Checklist — Pre-Launch

Before sharing the LMS URL with learners:

- [ ] All 11 HTML files are in the same folder
- [ ] `practitioner-toolkit.pdf` is in the same folder
- [ ] Open `index.html` and confirm all 10 module cards are visible
- [ ] Click through to at least one module and confirm the AI Tutor responds
- [ ] Complete a quiz and confirm the progress bar on `index.html` updates
- [ ] Test on mobile (or use browser DevTools mobile simulation)
- [ ] Confirm the toolkit download button on `b10.html` works
- [ ] Update the API key if the current key has expired or been rotated

---

## 10. Known Limitations and Notes

- **No B2 shortcut file:** Module B2 (Control Types) is served by `module-shell.html`. There is no `b2.html`. All links from other modules correctly point to `module-shell.html`.
- **Module unlock sequence:** Index.html enforces sequential unlock (each module requires the previous to be complete). Individual modules are directly accessible by URL regardless of completion state.
- **Certificate PDF:** The b10.html completion certificate is generated as a printable HTML certificate using `window.print()`. It is not a PDF download — learners print to PDF from their browser's print dialog.
- **AI Tutor rate limits:** Each module's tutor uses the same API key. Heavy concurrent usage may hit Anthropic rate limits. Monitor usage in the Anthropic console.

---

*KABO-TRN-CTL-WPB-001 · TIB Systems Inc. · tib-systems.com · academy@tib-systems.com*
