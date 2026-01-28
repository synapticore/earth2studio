# Fork Documentation Index

Welcome to the synapticore/earth2studio documentation suite. This index helps you navigate the comprehensive guides created for this fork.

## Quick Start

**New to this fork?** Start here:

1. Read the [README.md](README.md) for an overview
2. Check [KNOWN_ISSUES.md](KNOWN_ISSUES.md) for common problems
3. Follow [GPU_OPTIMIZATION.md](GPU_OPTIMIZATION.md) if you have consumer hardware
4. Review [LOCAL_DEPLOYMENT.md](LOCAL_DEPLOYMENT.md) for offline setups

## Documentation Suite

### For Users

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [README.md](README.md) | Project overview and quick start | First time learning about Earth2Studio |
| [KNOWN_ISSUES.md](KNOWN_ISSUES.md) | Common problems and workarounds | When you encounter an error |
| [GPU_OPTIMIZATION.md](GPU_OPTIMIZATION.md) | Running on 8-16GB GPUs | Using consumer hardware |
| [LOCAL_DEPLOYMENT.md](LOCAL_DEPLOYMENT.md) | Offline/air-gapped deployment | Working without internet access |

### For Developers & Contributors

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [CONTRIBUTING.md](CONTRIBUTING.md) | How to contribute code | Before submitting PRs |
| [FORK_GUIDE.md](FORK_GUIDE.md) | Fork maintenance best practices | Managing the fork |
| [ROADMAP.md](ROADMAP.md) | Development plans and goals | Understanding fork direction |

### For Maintainers

| Document | Purpose | When to Use |
|----------|---------|-------------|
| [REPOSITORY_CONFIG.md](REPOSITORY_CONFIG.md) | GitHub settings configuration | Setting up repository |
| [SECURITY.md](SECURITY.md) | Security policy and reporting | Handling vulnerabilities |

### Upstream Documentation

- [Official Earth2Studio Docs](https://nvidia.github.io/earth2studio/)
- [NVIDIA Repository](https://github.com/NVIDIA/earth2studio)
- [Installation Guide](https://nvidia.github.io/earth2studio/userguide/about/install.html)
- [User Guide](https://nvidia.github.io/earth2studio/userguide/)
- [API Documentation](https://nvidia.github.io/earth2studio/modules/)

## Document Overview

### FORK_GUIDE.md (303 lines, 8.3KB)

**For:** Fork maintainers and contributors  
**Contents:**
- Fork synchronization strategies
- GitHub repository configuration
- Community management guidelines
- Version control and tagging
- Contributing back to upstream
- Legal and licensing considerations

**Key Sections:**
- Defining fork mission and strategy
- Syncing with upstream workflow
- Managing fork-specific features
- CI/CD for forks
- Checklist for new maintainers

### LOCAL_DEPLOYMENT.md (654 lines, 16.6KB)

**For:** Users deploying in offline/air-gapped environments  
**Contents:**
- Complete offline deployment workflow
- Three-phase deployment process
- Model and dependency downloading
- Packaging and transfer strategies
- Docker containerization
- Troubleshooting offline issues

**Key Sections:**
- Phase 1: Downloading resources (models, dependencies, data)
- Phase 2: Setting up offline environment
- Phase 3: Running models locally
- Advanced topics (Docker, automation, optimization)
- Complete working examples

**Storage Requirements:** 50-100+ GB for complete setup

### GPU_OPTIMIZATION.md (564 lines, 14.3KB)

**For:** Users with consumer GPUs (8-24GB)  
**Contents:**
- Memory requirements by model
- 10 optimization strategies
- Hardware tier configurations
- Monitoring and profiling
- Complete optimized scripts

**Key Sections:**
- Memory optimization strategies (FP16, quantization, batching)
- Configuration for different GPU tiers (8GB, 12GB, 16GB, 24GB)
- Optimization checklist
- Memory monitoring tools
- Common issues and solutions

**Memory Savings:** Up to 75% with all optimizations

### KNOWN_ISSUES.md (474 lines, 13.1KB)

**For:** All users encountering problems  
**Contents:**
- 14+ documented issues with workarounds
- Upstream issue tracking
- Platform-specific issues
- Performance problems
- Best practices to avoid issues

**Key Sections:**
- Critical issues (GPU memory, dimension ordering)
- Model-specific issues (AIFS, GraphCast, NetCDF4)
- Data source issues (SST discrepancies, TimeWindow)
- Feature gaps (GenCast, restart functionality)
- Installation and platform issues

**Issue Coverage:**
- GPU memory: #649, #608
- AIFS installation: #647
- GraphCast limitations: #394
- NetCDF4: #546
- Others: #659, #478, #589, #201, #446, #587

### ROADMAP.md (321 lines, 8.6KB)

**For:** Contributors and community members  
**Contents:**
- Fork vision and goals template
- Short/medium/long-term planning
- Upstream issue tracking
- Release schedule
- Community engagement

**Key Sections:**
- Vision and goals (customizable)
- Upstream sync strategy
- Milestone planning (3-month, 6-month, 12+ month)
- Specific issue tracking
- Success metrics

**Purpose:** Living document for fork direction

### SECURITY.md (329 lines, 9.2KB)

**For:** Security researchers and users  
**Contents:**
- Vulnerability reporting process
- Security best practices for users
- Known security considerations
- Dependency security
- Disclosure policy

**Key Sections:**
- Reporting vulnerabilities (private process)
- User security best practices (API keys, data validation)
- Known security considerations (pickle files, ONNX, network data)
- Dependency scanning and updates
- Coordinated disclosure timeline

**Response Timeline:** 48 hours initial, 7 days assessment

### REPOSITORY_CONFIG.md (405 lines, 10KB)

**For:** Repository administrators  
**Contents:**
- 14 manual configuration steps
- GitHub settings walkthrough
- Branch protection setup
- Issue and discussion configuration
- GitHub Pages and Actions

**Key Sections:**
- Enable Issues and configure labels
- Add repository topics (15 recommendations)
- Set up Discussions with categories
- Configure branch protection
- GitHub Pages deployment
- Security settings
- Actions permissions

**Configuration Time:** ~30-60 minutes for complete setup

## Additional Resources

### Issue Templates

Located in `.github/ISSUE_TEMPLATE/`:

- **bug_report.yml** - Standard bug reports
- **feature_request.yml** - Feature requests
- **documentation_request.yml** - Documentation improvements
- **fork_specific.yml** - Fork-specific issues (NEW)
- **hardware_compatibility.yml** - Hardware compatibility reports (NEW)

### Existing Documentation

- [CHANGELOG.md](CHANGELOG.md) - Version history
- [CONTRIBUTING.md](CONTRIBUTING.md) - Contribution guidelines
- [LICENSE](LICENSE) - Apache 2.0 license
- [CITATION.cff](CITATION.cff) - Citation information

### Official Resources

- **Documentation:** https://nvidia.github.io/earth2studio/
- **Upstream Issues:** https://github.com/NVIDIA/earth2studio/issues
- **Model Hub:** https://huggingface.co/collections/nvidia/earth-2
- **NGC Catalog:** https://catalog.ngc.nvidia.com/

## Getting Help

### For Technical Issues

1. Check [KNOWN_ISSUES.md](KNOWN_ISSUES.md)
2. Search existing GitHub Issues
3. Review upstream NVIDIA/earth2studio issues
4. Create new issue using appropriate template

### For Questions

- Use GitHub Discussions (when enabled)
- Check FAQ in upstream documentation
- Community forums and channels

### For Security Issues

- **DO NOT** open public issues
- Follow [SECURITY.md](SECURITY.md) reporting process
- Contact maintainers privately

## Contributing to Documentation

We welcome documentation improvements:

1. **Typos/Corrections:** Open PR directly
2. **New Content:** Open issue first to discuss
3. **Translations:** Contact maintainers
4. **Examples:** Add to existing guides or propose new ones

### Documentation Standards

- Use Markdown format
- Include code examples
- Add tables for comparisons
- Link between documents
- Keep tone helpful and clear
- Update index when adding new docs

## Maintenance Schedule

### Regular Updates

- **Weekly:** Review new upstream issues
- **Monthly:** Update KNOWN_ISSUES.md
- **Quarterly:** Review and update ROADMAP.md
- **Annually:** Full documentation review

### Version-Triggered Updates

- **New Upstream Release:** Update sync status
- **Fork Release:** Update CHANGELOG and ROADMAP
- **Major Changes:** Update relevant guides

## Documentation Statistics

| Document | Lines | Size | Primary Audience |
|----------|-------|------|-----------------|
| FORK_GUIDE.md | 303 | 8.3KB | Maintainers |
| LOCAL_DEPLOYMENT.md | 654 | 16.6KB | Users |
| GPU_OPTIMIZATION.md | 564 | 14.3KB | Users |
| KNOWN_ISSUES.md | 474 | 13.1KB | Users |
| ROADMAP.md | 321 | 8.6KB | Contributors |
| SECURITY.md | 329 | 9.2KB | Security |
| REPOSITORY_CONFIG.md | 405 | 10KB | Admins |
| **Total New Documentation** | **3,050** | **~80KB** | **All** |

## Future Documentation Plans

Potential additions (track in ROADMAP.md):

- [ ] Hardware compatibility database
- [ ] Performance benchmarks by GPU
- [ ] Model comparison guide
- [ ] Beginner's tutorial series
- [ ] Video tutorials
- [ ] Jupyter notebook examples
- [ ] Docker compose examples
- [ ] Kubernetes deployment guide
- [ ] API reference (if fork adds APIs)
- [ ] FAQ compilation

## Feedback

We want to hear from you:

- **What's missing?** Open an issue
- **What's unclear?** Open an issue or PR
- **What's helpful?** Let us know in Discussions
- **Suggestions?** Always welcome!

---

**Last Updated:** 2026-01-28  
**Documentation Version:** 1.0  
**Maintainer:** synapticore team
