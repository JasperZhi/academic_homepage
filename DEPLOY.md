# Publishing the Academic Homepage to GitHub Pages

This guide walks through publishing `index.html` (and `cv.pdf`) as a static
site on GitHub Pages, under the URL `https://<username>.github.io/`.

## Step 0 — Prepare the files

The site folder must contain:

```
index.html   ← the homepage
cv.pdf       ← the CV file linked by the "Download CV" button
```

Copy your final CV PDF into this folder and rename it exactly `cv.pdf`
(lowercase, no spaces). GitHub Pages serves files with these names as-is.

## Step 1 — Create a GitHub repository

1. Log in to https://github.com.
2. Click the **+** icon (top-right) → **New repository**.
3. Repository name options:
   - **`<username>.github.io`** (e.g. `jasperxie.github.io`) → the site will
     live at `https://<username>.github.io/` — recommended for a personal page.
   - Any other name, e.g. `academic-homepage` → the site will live at
     `https://<username>.github.io/academic-homepage/`.
4. Set visibility to **Public** (required for free GitHub Pages).
5. Click **Create repository**. Do NOT initialize with a README (if you did,
   that is fine too — just pull first before pushing).

## Step 2 — Upload the files

Option A — Web upload (no command line):

1. On the new repository page, click **uploading an existing file** (or
   **Add file → Upload files**).
2. Drag `index.html` and `cv.pdf` into the upload area.
3. Commit message: `Add homepage` → click **Commit changes**.

Option B — Command line:

```bash
cd personal-homepage
git init
git add index.html cv.pdf
git commit -m "Add homepage"
git branch -M main
git remote add origin https://github.com/<username>/<repo-name>.git
git push -u origin main
```

## Step 3 — Enable GitHub Pages

1. In the repository, go to **Settings → Pages** (left sidebar).
2. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
3. Under **Branch**, choose **main** and **/ (root)**, then click **Save**.
4. Wait 1–3 minutes. A green banner shows the site URL once deployed.
   (If you used the `<username>.github.io` repo name, Pages is usually
   enabled automatically — verify at Settings → Pages.)

## Step 4 — Verify the site

1. Open `https://<username>.github.io/` (or the sub-path URL) in a browser.
2. Check every section renders and all internal anchors (#projects, etc.) jump
   correctly.
3. Click **Download CV** — `cv.pdf` must download without a 404.
4. Click the GitHub project link and confirm it opens the correct repository.
5. Test the mailto link.
6. On a phone (or browser dev tools, device mode ≈ 375px width), confirm the
   layout stays single-column and text is readable.
7. Toggle your OS appearance between Light and Dark mode and confirm both are
   readable.
8. Print preview (Cmd+P): the page should be black-on-white and the buttons
   hidden.

## Updating later

Edit `index.html`, commit, and push — GitHub Pages redeploys automatically
within a couple of minutes.
