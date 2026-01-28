# Security Policy

## Supported Versions

This fork of Earth2Studio is currently in active development. We support the following versions with security updates:

| Version | Supported          | Notes |
| ------- | ------------------ | ----- |
| Latest main branch | :white_check_mark: | Active development |
| Tagged releases | :white_check_mark: | Security fixes backported when critical |
| Older commits | :x: | Please update to latest |

## Upstream Security

As a fork of [NVIDIA/earth2studio](https://github.com/NVIDIA/earth2studio), we inherit security considerations from the upstream project. Please also check the [upstream repository](https://github.com/NVIDIA/earth2studio) for security advisories.

## Reporting a Vulnerability

### For Fork-Specific Vulnerabilities

If you discover a security vulnerability that is specific to this fork (not present in upstream), please report it privately:

**DO NOT** open a public issue for security vulnerabilities.

#### How to Report

1. **Email:** [Configure your email address here]
2. **Subject Line:** `[SECURITY] Brief description of vulnerability`
3. **Include:**
   - Description of the vulnerability
   - Steps to reproduce
   - Potential impact
   - Suggested fix (if you have one)
   - Your name/handle (if you want credit)

#### Response Timeline

- **Initial Response:** Within 48 hours of report
- **Assessment:** Within 7 days
- **Fix Timeline:** Depends on severity
  - Critical: 24-48 hours
  - High: 1 week
  - Medium: 2 weeks
  - Low: Next planned release

### For Upstream Vulnerabilities

If the vulnerability exists in the upstream NVIDIA/earth2studio project:

1. Report to NVIDIA following their security policy
2. Also notify us so we can coordinate updates
3. We will sync the fix from upstream once available

## Security Best Practices for Users

### Model Weights and Checkpoints

1. **Verify Sources:** Only download model weights from official sources:
   - NGC (NVIDIA GPU Cloud)
   - Official HuggingFace repositories
   - Official AWS S3 buckets
   - Verified upstream sources

2. **Checksum Verification:** When available, verify checksums of downloaded models:
   ```bash
   sha256sum model_checkpoint.pt
   # Compare with official checksum
   ```

3. **Isolated Environment:** Run Earth2Studio in isolated environments:
   ```bash
   # Use virtual environments
   python -m venv earth2studio-env
   
   # Or use Docker containers
   docker run --gpus all -it earth2studio:latest
   ```

### Data Sources

1. **API Keys:** Never commit API keys or credentials to version control
   ```python
   # BAD - don't do this
   api_key = "sk-abc123..."
   
   # GOOD - use environment variables
   import os
   api_key = os.environ.get('DATA_API_KEY')
   ```

2. **Data Validation:** Validate external data before use:
   ```python
   import numpy as np
   
   # Check for NaN or infinite values
   assert not np.any(np.isnan(data))
   assert not np.any(np.isinf(data))
   
   # Validate expected ranges
   assert data.min() >= expected_min
   assert data.max() <= expected_max
   ```

3. **Secure Connections:** When fetching data, use secure connections:
   ```python
   # Ensure HTTPS is used
   import requests
   response = requests.get(url, verify=True)  # Verify SSL certificates
   ```

### Code Execution

1. **Input Validation:** Validate all user inputs:
   ```python
   # Validate file paths
   from pathlib import Path
   file_path = Path(user_input).resolve()
   assert file_path.is_relative_to(allowed_directory)
   
   # Validate dates
   from datetime import datetime
   try:
       time = datetime.fromisoformat(user_time_input)
   except ValueError:
       raise ValueError("Invalid time format")
   ```

2. **Resource Limits:** Set limits to prevent resource exhaustion:
   ```python
   import torch
   
   # Limit GPU memory
   torch.cuda.set_per_process_memory_fraction(0.8)
   
   # Set timeouts for operations
   import signal
   signal.alarm(3600)  # 1 hour timeout
   ```

3. **Dependency Management:** Keep dependencies updated:
   ```bash
   # Check for known vulnerabilities
   pip install safety
   safety check
   
   # Update dependencies regularly
   pip install --upgrade earth2studio
   ```

### Model Inference

1. **Untrusted Models:** Be cautious with models from untrusted sources:
   - Models can contain malicious code in pickle files
   - Use safetensors format when possible
   - Inspect model files before loading

2. **Sandboxing:** Run untrusted inference in isolated environments:
   ```bash
   # Use containers with limited permissions
   docker run --gpus all --security-opt=no-new-privileges \
              --read-only earth2studio:latest
   ```

3. **Output Validation:** Validate model outputs:
   ```python
   # Check for anomalous outputs
   output = model(input)
   if output.abs().max() > threshold:
       raise ValueError("Model output exceeds expected range")
   ```

## Known Security Considerations

### 1. Pickle Files

Many PyTorch models use pickle for serialization, which can execute arbitrary code during deserialization.

**Mitigation:**
- Only load models from trusted sources
- Use `torch.jit` or `safetensors` format when possible
- Consider implementing custom model loaders

### 2. ONNX Runtime

ONNX Runtime has had security vulnerabilities in the past.

**Mitigation:**
- Keep ONNX Runtime updated to latest version
- Monitor ONNX Runtime security advisories
- Use containerized deployments

### 3. Network Data Sources

Data sources that fetch from external APIs or cloud storage may be vulnerable to man-in-the-middle attacks.

**Mitigation:**
- Always verify SSL certificates
- Use authenticated endpoints
- Validate data integrity with checksums
- Cache data locally after verification

### 4. GPU Memory Exhaustion

Malicious inputs could cause out-of-memory crashes.

**Mitigation:**
- Set memory limits: `torch.cuda.set_per_process_memory_fraction()`
- Validate input shapes before processing
- Implement timeouts for long-running operations

### 5. Path Traversal

File path inputs could be used for path traversal attacks.

**Mitigation:**
```python
from pathlib import Path

def safe_path(user_path, base_dir):
    """Ensure path is within allowed directory"""
    path = Path(base_dir) / Path(user_path)
    path = path.resolve()
    
    if not path.is_relative_to(Path(base_dir).resolve()):
        raise ValueError("Path traversal attempt detected")
    
    return path
```

## Dependency Security

### Scanning Dependencies

We use automated tools to scan for vulnerabilities:

```bash
# Using pip-audit
pip install pip-audit
pip-audit

# Using safety
pip install safety
safety check --json
```

### Dependency Updates

- **Critical Security Updates:** Applied immediately
- **High Severity:** Within 1 week
- **Medium/Low Severity:** Next scheduled release
- **Breaking Changes:** Evaluated case-by-case

## Security in CI/CD

Our CI/CD pipeline includes:

1. **Dependency Scanning:** Automated vulnerability scanning
2. **Code Analysis:** Static analysis for security issues
3. **License Compliance:** Checking for problematic licenses
4. **Container Scanning:** Docker image vulnerability scanning

## Disclosure Policy

### Coordinated Disclosure

We follow coordinated disclosure:

1. Report received and acknowledged
2. Assessment and fix development (private)
3. Testing and validation (private)
4. Public disclosure after fix is available
5. Credit given to reporter (if desired)

### Public Disclosure Timeline

- **Critical:** 7 days after fix is available
- **High:** 14 days after fix is available
- **Medium/Low:** 30 days after fix is available

## Security Advisories

Published security advisories will be available at:
- GitHub Security Advisories (this repository)
- KNOWN_ISSUES.md (for workarounds)
- Release notes (for fixed issues)

## Hall of Fame

We recognize security researchers who responsibly disclose vulnerabilities:

<!-- Add contributors here -->
- TBD

## Additional Resources

### Security Tools

- [pip-audit](https://github.com/pypa/pip-audit) - Python dependency auditing
- [safety](https://github.com/pyupio/safety) - Check Python dependencies for security issues
- [bandit](https://github.com/PyCQA/bandit) - Python code security analysis
- [trivy](https://github.com/aquasecurity/trivy) - Container security scanning

### Security Guidelines

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Python Security Best Practices](https://python.readthedocs.io/en/stable/library/security_warnings.html)
- [PyTorch Security](https://pytorch.org/docs/stable/notes/security.html)
- [NVIDIA Security](https://www.nvidia.com/en-us/security/)

### Upstream Security

Monitor upstream security:
- [NVIDIA Earth2Studio Repository](https://github.com/NVIDIA/earth2studio)
- [PyTorch Security Advisories](https://github.com/pytorch/pytorch/security/advisories)
- [Python CVEs](https://www.cvedetails.com/vulnerability-list/vendor_id-10210/Python.html)

## Questions?

For non-security questions, please use:
- GitHub Issues for bugs
- GitHub Discussions for questions
- Documentation for usage help

For security concerns, always use the private reporting methods above.

---

**Last Updated:** 2026-01-28  
**Next Review:** TBD
