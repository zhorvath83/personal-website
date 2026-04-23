# Personal Website - Claude AI Instructions


## Tech Stack

- **Hugo** (Go-based static site generator)
- **PaperMod theme** + hugo-notice shortcodes
- **JSON Resume** for CV generation
- **statichost.eu** (auto-deploy from main)
- **GitHub Actions** (CI/CD for CV build)
- **Pre-commit hooks** (auto-run on commit)

---

## Project Structure

```
personal-website/
├── hugo.yaml                  # Hugo config
├── go.mod                     # Theme dependencies
│
├── content/
│   ├── about.md               # About page
│   └── cv.md                  # CV page content
│
├── layouts/
│   ├── cv/
│   │   └── single.html        # CV page layout (PaperMod frame + iframe)
│   └── partials/
│       └── extend_head.html   # Custom <head> content (analytics)
│
├── resume/
│   ├── resume.json            # CV source (single source of truth)
│   └── resume.html            # CI-generated (intermediate)
│
├── static/
│   ├── resume/                # CI-generated (HTML + PDF)
│   │   ├── index.html         # Standalone CV HTML (iframe source)
│   │   └── Zoltan_Horvath_CV.pdf
│   └── img/                   # Images
│
├── public/                    # Hugo build output (git ignored)
└── resources/                 # Hugo cache (git ignored)
```

---

## Permission Matrix

### ✅ Auto-Allowed (Push Without Asking)

- **Content**: `content/about.md` edits
- **Images**: Adding/modifying `static/img/*` (follow IT naming)
- **Minor config**: `hugo.yaml` param changes (colors, titles, menu labels)
- **Theme customization**: `layouts/partials/extend_head.html` for analytics/custom head
- **CV layout**: `layouts/cv/single.html` modifications
- **Git ops**: Commit, push to main
- **Dependencies**: Merge Renovate PRs

### ⚠️ Ask First

- **CV changes**: ANY edit to `resume/resume.json`
- **Structural config**: Theme add/remove, new sections in `hugo.yaml`
- **CLAUDE.md**: This file modifications

### ❌ Never Touch

- **CI-generated**: `static/resume/*`, `resume.html`
- **Build artifacts**: `public/`, `resources/`, `.hugo_build.lock`

---

## Validation Rules (MANDATORY BEFORE COMMIT)

### Hugo Content/Config Changes
**ALWAYS run before commit:**
```bash
hugo server -D
# → Check http://localhost:1313/
# → User must validate visually in browser
```

### Resume Changes
**ALWAYS run before commit:**
```bash
# 1. JSON syntax check
cat resume/resume.json | jq empty

# 2. Schema validation
cd resume && resume validate && cd ..
```

**Rule**: No commit without successful validation!

---

## Development Workflow

### Local Commands

```bash
cd /Users/zhorvath83/Projects/personal/personal-website

# Start dev server
hugo server -D
# → http://localhost:1313/

# Build for production
hugo

# Clean build
rm -rf public/ resources/ && hugo
```

### 1. Content Changes (About Page)

```bash
# Edit content
vim content/about.md

# ⚠️ MANDATORY: Test locally BEFORE commit
hugo server -D
# → http://localhost:1313/
# User validates in browser

# After validation → push
git add content/about.md
git commit -m "update about page"
git push origin main
```

**Style**: Professional, not self-promotional.

**Available Markdown**:
```markdown
# Headers
**bold** *italic*
[links](https://url)
`code`

{{< notice info >}}Info box{{< /notice >}}
{{< notice warning >}}Warning{{< /notice >}}
{{< notice note >}}Note{{< /notice >}}
{{< notice tip >}}Tip{{< /notice >}}
```

### 2. CV Updates

```bash
# Edit JSON
vim resume/resume.json

# ⚠️ MANDATORY: Validate BEFORE commit
# 1. Validate JSON syntax
cat resume/resume.json | jq empty

# 2. Validate schema with resume-cli
cd resume && resume validate && cd ..

# ⚠️ STOP - Show changes to user → Get approval → Then push

git add resume/resume.json
git commit -m "chore(resume): description"
git push origin main

# CI auto-generates HTML + PDF in ~2 minutes
# Verify: https://horvathzoltan.me/cv/
```

### 3. Adding Images

```bash
# Naming: IT conventions (no spaces, no accents)
# ✅ OK: avatar_new.png, logo-2024.jpg
# ❌ BAD: új kép.png, avatar (final).jpg

cp ~/image.png static/img/profile_2024.png
git add static/img/profile_2024.png
git commit -m "add new image"
git push origin main
```

---

## Deployment Workflow

**Branch**: `main` (direct push)
**Commits**: Flexible style, many small commits OK

### Automated Pipeline

```
Push to main
    ↓
├─→ resume.json changed?
│   └─→ GitHub Action: Generate HTML + PDF → Commit to static/resume/
│
└─→ GitHub webhook → statichost.eu: hugo build → Deploy
```

**Monitoring**:
- GitHub Actions: Repository → Actions tab
- statichost.eu: Dashboard → personal-website

---

## URL Structure

- **Home**: https://horvathzoltan.me/
- **About**: https://horvathzoltan.me/about/
- **CV Page**: https://horvathzoltan.me/cv/ (PaperMod frame)
- **CV Standalone**: https://horvathzoltan.me/resume/ (iframe source)
- **CV PDF**: https://horvathzoltan.me/resume/Zoltan_Horvath_CV.pdf

---

## CV Architecture

The CV system uses a hybrid approach for optimal integration with PaperMod theme:

### Source of Truth
- **`resume/resume.json`**: JSON Resume format (single source)

### CI/CD Pipeline (GitHub Actions)
When `resume.json` changes:
1. Generate standalone HTML with `jsonresume-theme-reactive`
2. Apply print CSS fixes for PDF generation
3. Generate PDF from HTML using Puppeteer
4. Output:
   - `static/resume/index.html` (standalone, responsive)
   - `static/resume/Zoltan_Horvath_CV.pdf`

### Hugo Integration
- **`content/cv.md`**: CV page metadata
- **`layouts/cv/single.html`**: Custom layout with:
  - PaperMod header/footer (navigation, theme toggle)
  - Full-width iframe embedding `/resume/`
  - JavaScript theme sync (PaperMod ↔ iframe)
  - Floating PDF download button (PaperMod styled)

### Theme Synchronization
JavaScript automatically:
- Detects PaperMod theme changes (light/dark/auto)
- Reloads iframe and injects CSS overrides
- Maintains consistent look across theme toggles

### URL Structure
- `/cv` → Main CV page (PaperMod integrated)
- `/resume/` → Standalone HTML (iframe source, print-friendly)
- `/resume/Zoltan_Horvath_CV.pdf` → Downloadable PDF

---

## Security & Maintenance

### Pre-commit Hooks (Auto-run)
Checks on every commit:
- Large files (<5MB)
- Merge conflicts
- Private keys
- Secrets (gitleaks)
- Trailing whitespace (auto-fix)

### Safe to Commit
✅ Public data
✅ Social links, CV content

### Never Commit
❌ API keys, tokens, passwords
❌ Private keys, certificates
❌ Build artifacts, CI-generated files

---

## Troubleshooting

**Build fails locally**:
```bash
hugo check           # Verify config
hugo version         # Check version
rm -rf public/ resources/ && hugo
```

**CV workflow fails**:
```bash
cat resume/resume.json | jq empty  # Validate JSON
# Check GitHub Actions logs
```

**statichost.eu deploy fails**:
```bash
# Check statichost.eu Dashboard → Deployments
hugo check  # Usually config syntax error
```

---

## Decision Tree for Claude

```
┌─ Content change?
│  ├─ About page → Edit, test locally, push
│  └─ CV → Edit JSON, validate, ASK user, push
│
├─ Config change?
│  ├─ Minor (colors, labels) → Test, push
│  └─ Structural (themes) → ASK user first
│
├─ Asset change?
│  └─ Images → Follow naming rules, push
│
├─ Dependency update?
│  └─ Renovate PR → Can merge
│
└─ Documentation?
   └─ CLAUDE.md → ASK before modifying
```

---

## Notes for Claude

- Work in `/Users/zhorvath83/Projects/personal/personal-website`
- Use absolute paths always
- Test with `hugo server -D` before push
- User validates visual changes
- Push to `main` branch directly (no PR)
