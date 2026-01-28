# Earth2Studio Fork Maintenance Guide

This guide is intended for maintainers of the synapticore/earth2studio fork. It provides best practices and recommendations for managing this fork effectively.

## Fork Purpose and Strategy

### Defining Your Fork's Mission

As a fork of NVIDIA/earth2studio, it's important to clearly define:

1. **Purpose**: Why does this fork exist? What unique value does it provide?
   - Custom features for specific use cases?
   - Experimental features not yet ready for upstream?
   - Optimizations for specific hardware or environments?
   - Educational or research-focused modifications?

2. **Synchronization Strategy**: How will you stay in sync with upstream?
   - **Mirror Strategy**: Keep in sync with upstream, cherry-picking specific changes
   - **Divergence Strategy**: Maintain separate feature set, selectively merge upstream
   - **Hybrid Strategy**: Core features in sync, custom additions in separate modules

### Recommended: Document Your Approach

Create a clear statement in your README.md about:
- What makes this fork unique
- Which features differ from upstream
- Your plan for staying current with NVIDIA's releases
- Whether you accept community contributions

## GitHub Repository Configuration

### Essential Settings

Since this is a fresh fork, enable the following features through GitHub's web interface:

1. **Issues** (currently disabled):
   ```
   Settings → Features → Issues → ✓ Enable
   ```
   - Track fork-specific bugs and features
   - Use labels to distinguish from upstream issues

2. **Topics/Tags** for discoverability:
   ```
   Settings → About → Topics
   ```
   Recommended topics:
   - `weather-forecasting`
   - `climate-modeling`
   - `deep-learning`
   - `pytorch`
   - `ai-weather`
   - `earth-system-models`
   - `nvidia-fork` (or similar to indicate fork status)

3. **Discussions** (optional but recommended):
   ```
   Settings → Features → Discussions → ✓ Enable
   ```
   - Useful for community Q&A
   - Share use cases and best practices
   - Get feedback on fork direction

4. **Repository Description**:
   Update to distinguish from upstream:
   ```
   Fork of NVIDIA Earth2Studio with [your unique features/focus]
   ```

### Branch Protection

Configure branch protection for main branches:
```
Settings → Branches → Add branch protection rule
```

Recommended rules:
- Require pull request reviews
- Require status checks to pass (if CI is configured)
- Require branches to be up to date before merging

## Syncing with Upstream

### Initial Setup

Add upstream remote if not already configured:
```bash
git remote add upstream https://github.com/NVIDIA/earth2studio.git
git fetch upstream
```

### Regular Sync Workflow

1. **Check for upstream changes**:
   ```bash
   git fetch upstream
   git log HEAD..upstream/main --oneline
   ```

2. **Merge upstream changes**:
   ```bash
   git checkout main
   git merge upstream/main
   # Resolve any conflicts
   git push origin main
   ```

3. **Alternative: Rebase strategy** (keeps cleaner history):
   ```bash
   git checkout main
   git rebase upstream/main
   # Resolve conflicts if any
   git push origin main --force-with-lease
   ```

### Selective Merging

To cherry-pick specific features from upstream:
```bash
# Find the commit you want
git log upstream/main --oneline

# Cherry-pick it
git cherry-pick <commit-hash>
```

## Managing Fork-Specific Features

### Recommended Code Organization

Keep fork-specific code separate to ease upstream merging:

```
earth2studio/
  ├── models/          # Standard models (stay in sync)
  ├── fork_models/     # Fork-specific models
  ├── fork_utils/      # Fork-specific utilities
  └── ...
```

### Documenting Differences

Create a `FORK_DIFFERENCES.md` file listing:
- Custom features added
- Modified behaviors from upstream
- Removed features (if any)
- Custom dependencies

### Tagging Strategy

Use semantic versioning with a fork identifier:
- Upstream: `v0.12.0`
- Fork: `v0.12.0-fork.1`, `v0.12.0-fork.2`, etc.

## Contributing Back to Upstream

### When to Contribute Back

Consider contributing these types of changes to NVIDIA/earth2studio:
- Bug fixes
- Performance improvements
- New model wrappers
- Documentation improvements
- Test coverage enhancements

### Contribution Process

1. Test thoroughly in your fork
2. Create a clean branch from upstream main
3. Follow NVIDIA's contribution guidelines
4. Submit PR to NVIDIA/earth2studio
5. If accepted, merge upstream changes back to your fork

## Community Management

### Issue Management

1. **Label System**:
   - `upstream-tracking`: Issues also present in NVIDIA repo
   - `fork-specific`: Issues unique to this fork
   - `sync-needed`: Indicates upstream sync required

2. **Issue Templates**:
   - Use templates from `.github/ISSUE_TEMPLATE/`
   - Add fork-specific template if needed

3. **Triage Process**:
   - Weekly review of new issues
   - Close duplicates (link to upstream if applicable)
   - Tag appropriately

### Pull Request Guidelines

If accepting contributions:
1. Require signed commits (`git commit -s`)
2. Enforce code style (pre-commit hooks)
3. Require tests for new features
4. Document all API changes

## Continuous Integration

### Fork-Specific CI

Consider these modifications to CI:
1. **Custom test environments** for your hardware
2. **Additional validation** for fork-specific features
3. **Upstream compatibility checks** to detect divergence

### Example: GitHub Actions for Sync Check

Create `.github/workflows/upstream-sync-check.yml`:
```yaml
name: Upstream Sync Check
on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly
  workflow_dispatch:

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Fetch upstream
        run: |
          git remote add upstream https://github.com/NVIDIA/earth2studio.git
          git fetch upstream
      - name: Check for new commits
        run: |
          BEHIND=$(git rev-list --count HEAD..upstream/main)
          echo "Commits behind upstream: $BEHIND"
          if [ $BEHIND -gt 10 ]; then
            echo "::warning::Fork is $BEHIND commits behind upstream"
          fi
```

## Versioning Strategy

### Option 1: Track Upstream Versions
```
0.12.0-fork    # Based on upstream v0.12.0
0.12.0-fork.1  # Fork-specific patch
0.13.0-fork    # Updated to upstream v0.13.0
```

### Option 2: Independent Versioning
```
1.0.0  # Fork's first major release
1.1.0  # Fork's first minor update
```

Document your choice in README.md.

## Legal and Licensing

### Maintain Proper Attribution

- Keep all NVIDIA copyright notices
- Add your own for new files:
  ```python
  # SPDX-FileCopyrightText: Copyright (c) 2024-2025 NVIDIA CORPORATION & AFFILIATES.
  # SPDX-FileCopyrightText: Copyright (c) 2026 Synapticore
  # SPDX-License-Identifier: Apache-2.0
  ```

### Third-Party Dependencies

- Document any new dependencies
- Verify license compatibility
- Update LICENSE file if needed (but maintain Apache 2.0 for base)

## Resources

### Helpful Documentation

- [GitHub Fork Guide](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks)
- [Syncing a Fork](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork)
- [Semantic Versioning](https://semver.org/)
- [Developer Certificate of Origin](https://developercertificate.org/)

### Upstream Resources

- [NVIDIA Earth2Studio](https://github.com/NVIDIA/earth2studio)
- [NVIDIA Earth2Studio Docs](https://nvidia.github.io/earth2studio/)
- [NVIDIA Earth2 Hub](https://huggingface.co/collections/nvidia/earth-2)

## Checklist for New Fork Maintainers

- [ ] Enable GitHub Issues
- [ ] Add repository topics/tags
- [ ] Update repository description
- [ ] Configure branch protection rules
- [ ] Set up upstream remote
- [ ] Create ROADMAP.md with fork vision
- [ ] Update README.md with fork information
- [ ] Set up CI/CD for fork-specific needs
- [ ] Create contributor guidelines (if accepting PRs)
- [ ] Establish version numbering scheme
- [ ] Set up regular upstream sync schedule
- [ ] Create issue labels for fork management
- [ ] Consider enabling Discussions
- [ ] Document fork-specific features
- [ ] Plan initial release/tag strategy
