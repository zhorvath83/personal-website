# My Personal Website

This repository contains the source code for my personal website, which includes my resume and other relevant information about my professional background.

---

## Tech Stack

- **Hugo** - Go-based static site generator
- **PaperMod theme** - Modern, responsive Hugo theme
- **hugo-notice shortcodes** - Enhanced content formatting
- **JSON Resume** - Structured CV data format
- **Cloudflare Pages** - Hosting and auto-deployment from main branch
- **GitHub Actions** - CI/CD pipeline for CV generation
- **Pre-commit hooks** - Automated security and quality checks

---

## Project Structure

```
personal-website/
├── hugo.yaml                  # Hugo configuration
├── go.mod                     # Theme dependencies
│
├── content/
│   ├── about.md               # About page content
│   └── cv.md                  # CV page metadata
│
├── layouts/
│   ├── cv/
│   │   └── single.html        # CV page layout (PaperMod frame + iframe)
│   └── partials/
│       └── extend_head.html   # Custom <head> content (analytics)
│
├── resume/
│   ├── resume.json            # CV source (single source of truth)
│   └── resume.html            # CI-generated intermediate file
│
├── static/
│   ├── resume/                # CI-generated output
│   │   ├── index.html         # Standalone CV HTML (iframe source)
│   │   └── Zoltan_Horvath_CV.pdf
│   └── img/                   # Images and assets
│
├── public/                    # Hugo build output (git ignored)
└── resources/                 # Hugo cache (git ignored)
```

---

## CV Architecture

The CV system uses a hybrid approach for optimal integration with the PaperMod theme:

### Source of Truth
- **`resume/resume.json`**: JSON Resume format - single source for all CV outputs

### CI/CD Pipeline (GitHub Actions)
When `resume.json` changes, the pipeline automatically:
1. Generates standalone HTML using `jsonresume-theme-reactive`
2. Applies print CSS optimizations for PDF generation
3. Generates PDF from HTML using Puppeteer
4. Commits outputs to repository:
   - Standalone, responsive HTML
   - Downloadable PDF

### Hugo Integration
- **`content/cv.md`**: CV page metadata and Hugo front matter
- **`layouts/cv/single.html`**: Custom layout featuring:
  - PaperMod header/footer (navigation, theme toggle)
  - Full-width iframe embedding `/resume/`

### URL Structure
- `/cv` → Main CV page (PaperMod integrated, with navigation)
- `/resume/` → Standalone HTML (iframe source, print-friendly)
- `/resume/Zoltan_Horvath_CV.pdf` → Downloadable PDF version

---

## Deployment

This website is automatically deployed using [Cloudflare Pages](https://pages.cloudflare.com/).

**Deployment Workflow**:
1. Push changes
2. If `resume.json` changed: GitHub Action generates HTML + PDF
3. Cloudflare Pages builds Hugo site and deploys
4. Live site updates
