# Earth2Studio Copilot Instructions

## Repository Overview

**Earth2Studio** is a Python-based AI inference pipeline toolkit for weather and climate applications. It provides a unified API for weather/climate AI models, data sources, and scientific ML tooling. The package enables rapid prototyping of AI-driven weather forecasts and climate modeling workflows.

- **Type**: Python package (PyTorch-based AI/ML library)
- **Size**: ~2MB source code (129 Python files), 1.1MB tests (102 test files)
- **Languages**: Python 3.11+ (supports 3.11, 3.12, 3.13)
- **Key Frameworks**: PyTorch 2.5.0+, Xarray, Zarr, NumPy
- **Package Manager**: UV (Astral's fast Python package installer)
- **Build System**: Hatchling (PEP 517)
- **Testing**: Pytest with Tox
- **Linting**: Ruff, Black, MyPy (type-checked)
- **Documentation**: Sphinx with MyST and Sphinx-Gallery

## Environment Setup

### Initial Setup (REQUIRED)
```bash
# Install UV if not available
pip install uv

# Sync dependencies and create virtual environment
uv sync

# Install pre-commit hooks (for contributors)
uv run pre-commit install --install-hooks
```

**IMPORTANT**: Always run `uv sync` after pulling changes or before making code changes to ensure dependencies are up to date.

## Build, Test, and Validation Commands

### Formatting (ALWAYS run before committing)
```bash
# Format code with Black
uv run pre-commit run black -a --show-diff-on-failure
# OR use Makefile
make format
```

### Linting (ALWAYS run before committing)
```bash
# Run all linting checks
make lint
# This runs: ruff, mypy, markdownlint, and pre-commit hooks

# Individual linters:
uv run ruff check earth2studio/  # Code linting
uv run mypy earth2studio/        # Type checking
```

**Known Issues**: Ruff may show warnings about deprecated config in pyproject.toml (ignore these). Some test files may fail on systems without NVIDIA GPU (cuda:0 tests will fail - this is expected).

### Testing

**Quick Tests** (recommended for development, ~10-30 seconds):
```bash
uv run pytest test/utils/test_coords.py -v
# OR test specific directory
uv run pytest test/io/ -v
```

**Standard Test Suite** (required before PR):
```bash
# Using Makefile (recommended)
make pytest
# OR directly with tox
uvx tox -c tox-min.ini run

# This runs the minimal test suite across environments
# Estimated time: 2-5 minutes
```

**Full Test Suite** (CI equivalent):
```bash
make pytest-full
# OR
uvx tox -c tox.ini run -- -s --cov --cov-append --slow --package --testmon-noselect
# Estimated time: 10-30 minutes depending on network and model downloads
```

**Test Organization**:
- `test/io/` - IO backend tests
- `test/data/` - Data source tests
- `test/models/px/` - Prognostic model tests
- `test/models/dx/` - Diagnostic model tests
- `test/perturbation/` - Perturbation method tests
- `test/statistics/` - Statistical operation tests
- `test/utils/` - Utility function tests

**Test Markers**:
- `--slow`: Include slow-running tests
- `--package`: Include tests that download model packages
- `-m "not slow"`: Skip slow tests

### Coverage
```bash
make coverage
# Must achieve >90% coverage (enforced by CI)
```

### Documentation

**Build docs without examples** (fast, ~1-2 minutes):
```bash
make docs
# Output: docs/_build/html/index.html
```

**Build docs with examples** (slow, may take 30+ minutes):
```bash
make docs-full
```

**View documentation locally**:
```bash
cd docs/_build/html
python -m http.server 8000
# Open http://localhost:8000
```

### License Check
```bash
make license
# Verifies all source files have proper SPDX headers
```

## Project Structure

### Core Source Code (`earth2studio/`)
- `models/` - AI models for weather/climate prediction
  - `px/` - Prognostic models (time-stepping forecasts): FCN, AIFS, GraphCast, Pangu, etc.
  - `dx/` - Diagnostic models (time-independent): Precipitation, solar radiation, cyclone tracking
  - `nn/` - Neural network architectures
  - `auto/` - Automatic model loading utilities
- `data/` - Data sources (GFS, HRRR, ERA5, IFS, etc.)
- `io/` - IO backends (Zarr, NetCDF, XArray)
- `perturbation/` - Perturbation methods for ensemble forecasting
- `statistics/` - Statistical operations and metrics
- `utils/` - Utility functions (coordinates, time handling, types)
- `lexicon/` - Variable naming conventions across different data sources
- `run.py` - High-level inference workflows

### Tests (`test/`)
Mirror structure of `earth2studio/` with corresponding test files.

### Configuration Files
- `pyproject.toml` - **Main configuration** (dependencies, build, tools, type hints)
- `tox.ini` - Test environment configurations (40+ test environments)
- `tox-min.ini` - Minimal test suite for quick validation
- `.pre-commit-config.yaml` - Pre-commit hook configuration
- `Makefile` - Common development commands
- `setup.py` - Minimal setuptools shim (defers to pyproject.toml)

### Additional Directories
- `docs/` - Sphinx documentation source
- `examples/` - Example scripts (rendered in docs via Sphinx-Gallery)
- `recipes/` - Advanced workflow recipes (HENS, S2S)
- `.github/workflows/` - CI/CD configuration (Blossom-CI)

## Code Style Requirements

### Type Hints (REQUIRED)
- All functions MUST have type hints for parameters and return values
- Use modern Python 3.10+ syntax: `list[str]` not `List[str]`, `|` not `Union`
- Common types defined in `earth2studio/utils/type.py`
- MyPy enforced with `disallow_untyped_defs = true`

### Formatting
- **Black** formatter (version 24.1.0)
- Line length: Auto-configured by Black
- Files: lowercase with underscores
- Classes: CamelCase
- Functions: snake_case

### Docstrings (REQUIRED)
- NumPy style docstrings for all public APIs
- Type hints in docstrings
- Optional args marked with "optional" and default values
- Interrogate enforces >95% docstring coverage

### Licensing (REQUIRED)
All source files must start with:
```python
# SPDX-FileCopyrightText: Copyright (c) 2024-2025 NVIDIA CORPORATION & AFFILIATES.
# SPDX-FileCopyrightText: All rights reserved.
# SPDX-License-Identifier: Apache-2.0
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
```

### Commits (REQUIRED)
All commits must be signed off with `git commit -s` to certify Developer Certificate of Origin.

## Continuous Integration

### GitHub Workflow
- **Primary CI**: Blossom-CI (`.github/workflows/blossom-ci.yml`)
- **Trigger**: Comment `/blossom-ci` on PR (requires authorization)
- **Steps**: Vulnerability scan → CI job execution
- CI runs on self-hosted runners with GPU support

### CI Pipeline Checks
1. Pre-commit hooks (Black, Ruff, MyPy, Interrogate, Markdownlint)
2. License header verification
3. Test suite (tox environments)
4. Coverage check (must be >90%)
5. Documentation build

### Pre-commit Hooks
Configured in `.pre-commit-config.yaml`:
- black: Code formatting
- ruff: Linting with auto-fix
- mypy: Type checking
- interrogate: Docstring coverage
- markdownlint: Markdown formatting
- pyupgrade: Python 3.10+ syntax enforcement
- uv-lock: UV lock file updates

## Common Workflows

### Adding a New Feature
1. Run `uv sync` to ensure up-to-date environment
2. Create feature in appropriate module
3. Add type hints and NumPy-style docstrings
4. Add tests in corresponding `test/` directory (>90% coverage)
5. Run `make format` and `make lint`
6. Run `make pytest` to validate tests pass
7. Add example if applicable (in `examples/`)
8. Commit with sign-off: `git commit -s -m "message"`

### Adding a New Model
1. Choose model type: prognostic (`models/px/`) or diagnostic (`models/dx/`)
2. Implement model class with required protocols
3. Add tests in `test/models/px/` or `test/models/dx/`
4. Add tox environment in `tox.ini` for model-specific dependencies
5. Update `pyproject.toml` with optional dependencies if needed
6. Notify maintainers for CI pipeline integration

### Debugging Test Failures
- Use `pytest -v` for verbose output
- Use `pytest -s` to see print statements
- Use `pytest -k test_name` to run specific tests
- CUDA tests will fail on non-GPU systems (expected)
- Network tests may timeout on slow connections (use `--slow` flag)

## Important Notes

1. **UV is REQUIRED**: This project uses UV for dependency management, not pip or conda directly
2. **GPU Optional**: Most functionality works on CPU; GPU tests will xfail on non-GPU systems
3. **Model Downloads**: Some tests download large model files (~10GB cache); set `EARTH2STUDIO_CACHE` env var to control cache location
4. **Examples as Tests**: Examples in `examples/` are executed as tests during full docs build
5. **Recipes vs Examples**: Recipes are advanced workflows (not turnkey); Examples are simpler tutorials
6. **Network Dependencies**: Data source tests may fail with slow/unstable network connections

## Quick Reference Commands

```bash
# Setup
uv sync

# Development
make format        # Format code
make lint          # Run all linters
make pytest        # Run test suite
make coverage      # Check coverage
make docs          # Build documentation

# Testing
uv run pytest test/ -v                    # Run all tests
uv run pytest test/io/ -v                 # Test specific module
uvx tox -c tox-min.ini run                # Run minimal tox suite
uvx tox -c tox.ini run                    # Run full tox suite

# Pre-commit
uv run pre-commit run --all-files         # Run all hooks
uv run pre-commit run black -a            # Run Black only
```

## Trust These Instructions

These instructions have been validated against the actual codebase and successfully tested commands. Trust them first, and only search for additional information if:
- A command fails with an error not documented here
- You need details about a specific model or data source API
- You need information about a feature not covered in this guide
