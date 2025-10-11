# Personal Website - Claude AI Instructions

**Working Directory**: `/Users/zhorvath83/Projects/personal/personal-website`  
**Live Site**: https://horvathzoltan.me  
**Repository**: https://github.com/zhorvath83/private-website

---

## Tech Stack

- **Hugo** (Go-based static site generator)
- **PaperMod theme** + hugo-notice shortcodes
- **JSON Resume** for CV generation
- **Cloudflare Pages** (auto-deploy from main)
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
│   └── about/index.md         # About page
│
├── layouts/
│   └── partials/
│       └── extend_head.html   # Custom <head> content (analytics)
│
├── resume/
│   ├── resume-en.json         # CV source
│   └── resume-en.html         # CI-generated
│
├── static/
│   ├── cv/                    # CI-generated (HTML + PDF)
│   └── img/                   # Images
│
├── public/                    # Hugo build output (git ignored)
└── resources/                 # Hugo cache (git ignored)
```

---

## Permission Matrix

### ✅ Auto-Allowed (Push Without Asking)

- **Content**: `content/about/index.md` edits
- **Images**: Adding/modifying `static/img/*` (follow IT naming)
- **Minor config**: `hugo.yaml` param changes (colors, titles, menu labels)
- **Theme customization**: `layouts/partials/extend_head.html` for analytics/custom head
- **Git ops**: Commit, push to main
- **Dependencies**: Merge Renovate PRs

### ⚠️ Ask First

- **CV changes**: ANY edit to `resume/resume-en.json`
- **Structural config**: Theme add/remove, new sections in `hugo.yaml`
- **CLAUDE.md**: This file modifications

### ❌ Never Touch

- **CI-generated**: `static/cv/*`, `resume-en.html`
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
cat resume/resume-en.json | jq empty

# 2. Schema validation
cd resume && cp resume-en.json resume.json && resume validate && rm resume.json && cd ..
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
vim content/about/index.md

# ⚠️ MANDATORY: Test locally BEFORE commit
hugo server -D
# → http://localhost:1313/
# User validates in browser

# After validation → push
git add content/about/index.md
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
vim resume/resume-en.json

# ⚠️ MANDATORY: Validate BEFORE commit
# 1. Validate JSON syntax
cat resume/resume-en.json | jq empty

# 2. Validate schema with resume-cli
cd resume && cp resume-en.json resume.json && resume validate && rm resume.json && cd ..

# ⚠️ STOP - Show changes to user → Get approval → Then push

git add resume/resume-en.json
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
├─→ resume-en.json changed?
│   └─→ GitHub Action: Generate HTML + PDF → Commit to static/cv/
│
└─→ Cloudflare Pages: hugo build → Deploy
```

**Monitoring**:
- GitHub Actions: Repository → Actions tab
- Cloudflare: Dashboard → Pages → personal-website

---

## URL Structure

- **Home**: https://horvathzoltan.me/
- **About**: https://horvathzoltan.me/about/
- **CV HTML**: https://horvathzoltan.me/cv/
- **CV PDF**: https://horvathzoltan.me/cv/Zoltan_Horvath_en_CV.pdf

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
✅ Public email: contact@horvathzoltan.me  
✅ Public phone: +36 (30) 946 1662  
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
cat resume/resume-en.json | jq empty  # Validate JSON
# Check GitHub Actions logs
```

**Cloudflare deploy fails**:
```bash
# Check Cloudflare Dashboard → Pages → Deployments
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
