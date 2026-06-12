# EduHubBengal - Hosting and Running on GitHub

This website is a highly optimized, responsive preparation platform for JEE. It runs on a local Node.js dev server, but is fully static and optimized for deployment to **GitHub Pages**.

---

## Local Development (Node.js Dev Server)

To run the site locally using Node.js, follow these steps:

1. **Install Node.js** (if not already installed).
2. **Open your terminal** in the project directory.
3. **Install dependencies**:
   ```bash
   npm install
   ```
4. **Run the local dev server**:
   ```bash
   npm run dev
   ```
5. **Open in browser**:
   Navigate to `http://localhost:5173/` to see the site.

---

## Hosting on GitHub Pages (Recommended)

GitHub Pages hosts static websites directly from a GitHub repository for free. Since this project is fully client-side (Tailwind CSS and fonts are served via CDNs, and the Node server is only for local dev), you can host it directly on GitHub Pages!

### Why this works:
We optimized the directory routing:
* `material/material.html` was renamed to `material/index.html`
* `contact/contact.html` was renamed to `contact/index.html`

On web servers like GitHub Pages, links to `material/` or `contact/` will automatically look for and serve the `index.html` file inside those folders. This makes the URL routing work seamlessly out-of-the-box.

### Option A: Standard Deployment (Super Simple)
1. **Create a GitHub repository** and push your code to it (e.g., to the `main` branch).
2. Go to your repository settings on GitHub:
   * **Settings** (tab) -> **Pages** (left sidebar).
3. Under **Build and deployment**:
   * **Source**: Select `Deploy from a branch`.
   * **Branch**: Select `main` (or whichever branch you push to) and `/ (root)` folder.
   * Click **Save**.
4. GitHub will deploy the site. Within a minute, your site will be live at `https://<username>.github.io/<repository-name>/`.

---

### Option B: Automated Build and Deploy (Using GitHub Actions)
If you want GitHub to automatically build and bundle your site using Vite whenever you push changes, you can use a GitHub Actions workflow.

1. Create a directory structure `.github/workflows/` at the root of the project.
2. Create a file named `deploy.yml` inside it.
3. Paste the following configuration:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches:
      - main  # Trigger workflow on pushes to main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - name: Install Dependencies
        run: npm ci

      - name: Build with Vite
        run: npm run build

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload Artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'  # Upload the Vite build output folder

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

4. Push this file to GitHub, go to your repository's **Settings -> Pages**, and under **Source**, select **GitHub Actions** instead of "Deploy from a branch".
5. GitHub will now automatically run the workflow, compile your assets using Vite, and deploy your site on every push!
