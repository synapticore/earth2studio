# Repository Configuration Guide

This guide provides step-by-step instructions for configuring the GitHub repository settings that cannot be automated through code.

## Overview

Many repository improvements require manual configuration through GitHub's web interface. This guide walks through each setting that should be configured for this fork.

## Prerequisites

- Repository admin access
- GitHub account logged in
- Web browser

---

## 1. Enable Issues

Issues are currently disabled. Enable them to track fork-specific work.

**Steps:**

1. Go to repository: `https://github.com/synapticore/earth2studio`
2. Click **Settings** tab
3. Scroll to **Features** section
4. Check ✓ **Issues**
5. Click **Save**

**Recommended Issue Labels:**

After enabling, add these custom labels:

| Label | Color | Description |
|-------|-------|-------------|
| `fork-specific` | `#d73a4a` | Issues specific to this fork |
| `upstream-tracking` | `#0075ca` | Issues also present in NVIDIA repo |
| `sync-needed` | `#fbca04` | Requires upstream sync |
| `hardware-compatibility` | `#7057ff` | Hardware compatibility reports |
| `memory-optimization` | `#008672` | Memory/GPU optimization related |
| `documentation` | `#0075ca` | Documentation improvements |
| `good-first-issue` | `#7057ff` | Good for newcomers |

---

## 2. Add Repository Topics

Topics help users discover your repository.

**Steps:**

1. Go to repository main page
2. Click the ⚙️ gear icon next to **About** section (top right)
3. In **Topics** field, add the following (space or comma separated):
   ```
   weather-forecasting
   climate-modeling
   deep-learning
   pytorch
   ai-weather
   earth-system-models
   nvidia-fork
   weather-prediction
   climate-science
   machine-learning
   gpu-optimization
   scientific-computing
   pytorch-models
   weather-models
   ai-inference
   ```
4. Click **Save changes**

**Recommended Topics Priority:**

Essential topics:
- `weather-forecasting`
- `climate-modeling`
- `deep-learning`
- `pytorch`
- `ai-weather`

Supplementary topics:
- `earth-system-models`
- `weather-prediction`
- `gpu-optimization`
- `scientific-computing`

---

## 3. Update Repository Description

The current description should distinguish this fork from upstream.

**Steps:**

1. Go to repository main page
2. Click the ⚙️ gear icon next to **About** section
3. Update **Description** to:
   ```
   Fork of NVIDIA Earth2Studio with enhanced documentation and optimizations for consumer hardware. Run AI weather models on 8-16GB GPUs with comprehensive guides for local deployment.
   ```
4. Update **Website** (optional) to your documentation site or keep upstream link
5. Check ✓ **Releases** (if you'll publish releases)
6. Check ✓ **Packages** (if applicable)
7. Click **Save changes**

---

## 4. Enable GitHub Discussions

Discussions provide a community Q&A platform.

**Steps:**

1. Go to **Settings** tab
2. Scroll to **Features** section
3. Check ✓ **Discussions**
4. Click **Set up discussions**
5. Choose initial categories:
   - 📣 Announcements
   - 💬 General
   - 💡 Ideas & Feature Requests
   - 🙏 Q&A
   - 🛠️ Hardware & Optimization
   - 📚 Show & Tell

**Discussion Category Descriptions:**

- **Announcements**: Fork updates and releases
- **General**: General discussion about Earth2Studio
- **Ideas & Feature Requests**: Propose new features
- **Q&A**: Get help from the community
- **Hardware & Optimization**: Discuss hardware compatibility and optimization strategies
- **Show & Tell**: Share your projects and results

---

## 5. Configure Branch Protection

Protect main branch from accidental changes.

**Steps:**

1. Go to **Settings** → **Branches**
2. Click **Add branch protection rule**
3. Branch name pattern: `main`
4. Enable these rules:
   - ✓ **Require a pull request before merging**
     - ✓ Require approvals: 1
   - ✓ **Require status checks to pass before merging**
     - Search and add: `build`, `test`, `lint` (if CI is set up)
     - ✓ Require branches to be up to date before merging
   - ✓ **Require conversation resolution before merging**
   - ✓ **Do not allow bypassing the above settings** (optional, for strict workflow)
5. Click **Create** or **Save changes**

---

## 6. Set Up GitHub Pages (Optional)

Host documentation on GitHub Pages.

**Steps:**

1. Go to **Settings** → **Pages**
2. Under **Source**, select:
   - Branch: `gh-pages` (if you have one) or `main`
   - Folder: `/ (root)` or `/docs`
3. Click **Save**
4. Wait for deployment (usually 1-2 minutes)
5. Your site will be available at: `https://synapticore.github.io/earth2studio/`

**For Sphinx Documentation:**

If using Sphinx docs:
1. Build docs locally: `make docs`
2. Copy `docs/_build/html/` contents to `gh-pages` branch
3. Push to GitHub

**GitHub Actions for Auto-Deploy:**

Create `.github/workflows/docs.yml`:
```yaml
name: Deploy Documentation
on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: '3.12'
      - name: Install dependencies
        run: |
          pip install uv
          uv sync
      - name: Build docs
        run: make docs
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: docs/_build/html
  
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## 7. Configure Funding (Optional)

If accepting sponsorships or donations.

**Steps:**

1. Create `.github/FUNDING.yml` in repository (already created in this PR)
2. Edit to add your funding platforms:
   ```yaml
   # Example FUNDING.yml
   github: [your-github-username]
   patreon: your-patreon
   ko_fi: your-kofi
   custom: ["https://your-website.com/donate"]
   ```
3. Commit and push
4. A "Sponsor" button will appear on your repository

---

## 8. Configure Security Settings

**Steps:**

1. Go to **Settings** → **Security** → **Code security and analysis**
2. Enable recommended features:
   - ✓ **Dependency graph** (usually enabled by default)
   - ✓ **Dependabot alerts**
   - ✓ **Dependabot security updates**
   - ✓ **Grouped security updates** (optional)
3. Click **Enable** for each

**Security Advisories:**

1. Go to **Security** tab
2. Click **Advisories**
3. You can now create security advisories when needed

---

## 9. Configure Repository Visibility (If Needed)

If you need to change repository visibility:

**Steps:**

1. Go to **Settings**
2. Scroll to **Danger Zone** at bottom
3. Click **Change visibility**
4. Choose: Public / Private
5. Follow confirmation prompts

**Note:** This repository should remain **Public** to match upstream licensing.

---

## 10. Configure Merge Button

Control which merge strategies are allowed.

**Steps:**

1. Go to **Settings** → **General**
2. Scroll to **Pull Requests** section
3. Configure merge button options:
   - ✓ **Allow merge commits** (recommended)
   - ✓ **Allow squash merging** (recommended)
   - ⬜ **Allow rebase merging** (optional)
4. Enable:
   - ✓ **Automatically delete head branches** (cleans up after merge)
5. Click **Save**

---

## 11. Set Up Repository Insights

GitHub Insights are automatically available but can be customized:

**Steps:**

1. Go to **Insights** tab
2. Review default metrics
3. No configuration needed, but familiarize yourself with:
   - Contributors graph
   - Commit activity
   - Code frequency
   - Dependency graph
   - Network graph

---

## 12. Configure Actions Permissions

If using GitHub Actions:

**Steps:**

1. Go to **Settings** → **Actions** → **General**
2. Under **Actions permissions**, choose:
   - ✓ **Allow all actions and reusable workflows** (or)
   - ✓ **Allow [your organization] and select non-[your organization] actions**
3. Under **Workflow permissions**, choose:
   - ✓ **Read and write permissions** (for auto-documentation)
4. Click **Save**

---

## 13. Configure Notifications

Personal setting, but recommended for maintainers:

**Steps:**

1. Go to repository main page
2. Click **Watch** button (top right)
3. Choose notification level:
   - **All Activity** (for active maintainers)
   - **Custom** → Select: Issues, Pull Requests, Releases

---

## 14. Create Milestone for Fork Development

Track progress toward releases:

**Steps:**

1. Go to **Issues** → **Milestones**
2. Click **New milestone**
3. Create milestones like:
   - `v0.1-alpha` - Initial fork setup
   - `v0.5-beta` - First feature release
   - `v1.0` - First stable release
4. Add due dates (optional)
5. Assign issues to milestones as they're created

---

## Verification Checklist

After completing all configurations:

- [ ] Issues enabled
- [ ] Repository topics added (at least 5)
- [ ] Description updated and distinguishes from upstream
- [ ] Discussions enabled (optional but recommended)
- [ ] Branch protection configured for `main`
- [ ] GitHub Pages set up (optional)
- [ ] Funding configured (optional)
- [ ] Security features enabled
- [ ] Merge button options configured
- [ ] Actions permissions set
- [ ] Notifications configured
- [ ] Initial milestones created

---

## Maintenance

These settings should be reviewed:

- **Quarterly**: Review branch protection rules, security alerts
- **Semi-annually**: Update topics, review labels
- **Annually**: Review all settings for GitHub feature updates

---

## Getting Help

If you encounter issues:
- GitHub Settings Documentation: https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features
- GitHub Community: https://github.community/
- GitHub Support: https://support.github.com/

---

**Last Updated:** 2026-01-28  
**Next Review:** TBD
