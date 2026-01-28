# Known Issues and Workarounds

This document tracks known issues in Earth2Studio, including those from the upstream NVIDIA repository and fork-specific issues. Each issue includes workarounds when available.

**Last Updated:** 2026-01-28

## Critical Issues

### GPU Memory Limitations on Consumer Hardware

**Issue:** Models exceed memory limits on GPUs with 16GB or less  
**Upstream References:** [#649](https://github.com/NVIDIA/earth2studio/issues/649), [#608](https://github.com/NVIDIA/earth2studio/issues/608)  
**Status:** Known limitation  
**Affected Models:** Most large models (GraphCast Operational, AIFS, Pangu)

**Description:**
Many Earth2Studio models were designed for data center GPUs (40GB+) and cannot run on consumer GPUs without modifications.

**Workarounds:**

1. **Use FP16 precision** (reduces memory by ~50%):
   ```python
   model = model.half()
   input_tensor = input_tensor.half()
   ```

2. **Use smaller model variants**:
   ```python
   # Instead of GraphCast Operational
   from earth2studio.models.px import GraphCastSmall
   model = GraphCastSmall.load_model(GraphCastSmall.load_default_package())
   ```

3. **Process timesteps sequentially** instead of batching

4. **Apply model quantization** for extreme cases:
   ```python
   quantized_model = torch.quantization.quantize_dynamic(
       model, {torch.nn.Linear}, dtype=torch.qint8
   )
   ```

See [GPU_OPTIMIZATION.md](./GPU_OPTIMIZATION.md) for comprehensive strategies.

---

### Dimension Ordering Issues in Batched Workflows

**Issue:** Incorrect dimension handling for batched input data in deterministic workflows  
**Upstream Reference:** [#659](https://github.com/NVIDIA/earth2studio/issues/659)  
**Status:** Under investigation  
**Affected Components:** `earth2studio.run.deterministic`

**Description:**
When processing multiple initial conditions simultaneously, dimension ordering may be incorrect, leading to wrong forecasts or crashes.

**Workaround:**

1. **Process initial conditions separately** instead of batching:
   ```python
   # Instead of batching
   # times = ["2025-01-01T00:00:00", "2025-01-02T00:00:00"]
   
   # Process one at a time
   for time in ["2025-01-01T00:00:00", "2025-01-02T00:00:00"]:
       run([time], nsteps=10, model=model, data=data, io=io)
   ```

2. **Explicitly reshape tensors** before passing to model:
   ```python
   # Ensure correct dimension order: (batch, time, variable, lat, lon)
   input_tensor = input_tensor.permute(0, 1, 2, 3, 4)
   ```

---

## Model-Specific Issues

### AIFS Model Installation Failures (PyTorch 2.10+)

**Issue:** AIFSENS fails to install with PyTorch 2.10 and later  
**Upstream Reference:** [#647](https://github.com/NVIDIA/earth2studio/issues/647)  
**Status:** Version compatibility issue  
**Affected Models:** AIFS, AIFSENS

**Description:**
Flash Attention dependency has compatibility issues with PyTorch 2.10+.

**Workaround:**

1. **Use PyTorch 2.5.x**:
   ```bash
   pip install torch==2.5.1
   pip install earth2studio[aifs]
   ```

2. **Use pre-built Flash Attention wheels**:
   ```bash
   # Download pre-built wheel for your Python/CUDA version
   pip install flash-attn --no-build-isolation -f https://flashattn.dev/
   pip install earth2studio[aifs]
   ```

3. **Use NVIDIA PyTorch container** (includes Flash Attention):
   ```bash
   docker run -it nvcr.io/nvidia/pytorch:25.12-py3
   ```

---

### GraphCast Operational Cannot Handle Multiple Timestamps

**Issue:** GraphCastOperational fails when given multiple initial timestamps  
**Upstream Reference:** [#394](https://github.com/NVIDIA/earth2studio/issues/394)  
**Status:** Known limitation  
**Affected Models:** GraphCastOperational

**Description:**
Unlike other models, GraphCastOperational expects exactly one initial timestamp.

**Workaround:**

1. **Loop over timestamps manually**:
   ```python
   for timestamp in ["2025-01-01T00:00:00", "2025-01-02T00:00:00"]:
       run([timestamp], nsteps=4, model=model, data=data, io=io)
   ```

2. **Use GraphCastSmall** if multiple timestamps are needed:
   ```python
   from earth2studio.models.px import GraphCastSmall
   # This variant may handle multiple timestamps better
   ```

---

### NetCDF4 Backend Synchronization Issues

**Issue:** Concurrent writes to NetCDF4Backend cause data corruption or hangs  
**Upstream Reference:** [#546](https://github.com/NVIDIA/earth2studio/issues/546)  
**Status:** Known limitation of NetCDF4  
**Affected Components:** `earth2studio.io.NetCDF4Backend`

**Description:**
NetCDF4 library has limitations with concurrent writes, especially in parallel workflows.

**Workaround:**

1. **Use ZarrBackend instead** (recommended):
   ```python
   from earth2studio.io import ZarrBackend
   io = ZarrBackend("output.zarr")
   ```

2. **Use AsyncZarrBackend** for parallel workflows:
   ```python
   from earth2studio.io import AsyncZarrBackend
   io = AsyncZarrBackend("output.zarr")
   ```

3. **If NetCDF4 is required**, disable parallel writes:
   ```python
   # Set environment variable before import
   import os
   os.environ['HDF5_USE_FILE_LOCKING'] = 'FALSE'
   ```

---

## Data Source Issues

### Sea Surface Temperature Discrepancies

**Issue:** SST values differ between data sources and model outputs  
**Upstream Reference:** [#478](https://github.com/NVIDIA/earth2studio/issues/478)  
**Status:** Under investigation  
**Affected Components:** Various data sources

**Description:**
Sea surface temperature values may not match between different data sources (GFS, ERA5, IFS) due to different processing or units.

**Workaround:**

1. **Verify units** before comparison:
   ```python
   # Ensure temperature is in Kelvin
   sst_kelvin = sst_celsius + 273.15
   ```

2. **Use consistent data source** for initial conditions and validation

3. **Check variable names** in lexicon:
   ```python
   from earth2studio.lexicon import base
   # Verify SST variable naming
   print(base.SST)
   ```

---

### TimeWindow Data Source Test Coverage

**Issue:** Insufficient test coverage for TimeWindow data source  
**Upstream Reference:** [#589](https://github.com/NVIDIA/earth2studio/issues/589)  
**Status:** Testing gap identified  
**Affected Components:** `earth2studio.data.TimeWindow`

**Description:**
TimeWindow data source lacks comprehensive testing, potentially hiding edge cases.

**Workaround:**

1. **Validate outputs manually** when using TimeWindow:
   ```python
   from earth2studio.data import TimeWindow
   
   # Create TimeWindow
   tw = TimeWindow(base_source, window_size=3)
   
   # Validate output dimensions
   output = tw(time, variables)
   assert output.shape == expected_shape, "Dimension mismatch!"
   ```

2. **Test with known data** before production use

---

## Feature Gaps

### Missing GenCast Model

**Issue:** GenCast weather prediction model not yet implemented  
**Upstream Reference:** [#201](https://github.com/NVIDIA/earth2studio/issues/201)  
**Status:** Feature request  
**Impact:** Users cannot use DeepMind's latest weather model

**Workaround:**
- Use GraphCast or AIFS as alternatives
- Implement custom wrapper if model weights are available
- Track upstream issue for updates

---

### No Restart Functionality for Long Simulations

**Issue:** Cannot checkpoint and resume long-running simulations  
**Upstream Reference:** [#446](https://github.com/NVIDIA/earth2studio/issues/446)  
**Status:** Feature request  
**Impact:** Long simulations must complete in one run

**Workaround:**

1. **Manual checkpointing**:
   ```python
   import torch
   
   # Save state after each N steps
   for step in range(0, total_steps, checkpoint_interval):
       output = model(input)
       torch.save({
           'step': step,
           'model_state': model.state_dict(),
           'output': output
       }, f'checkpoint_{step}.pt')
   ```

2. **Use Zarr for incremental writes**:
   ```python
   # Zarr automatically saves progress
   io = ZarrBackend("simulation.zarr")
   # Can resume by reading last written step
   ```

---

### Limited Windowed Input Diagnostic Support

**Issue:** Diagnostic models don't fully support windowed inputs  
**Upstream Reference:** [#587](https://github.com/NVIDIA/earth2studio/issues/587)  
**Status:** Feature gap  
**Affected Components:** Various diagnostic models

**Description:**
Some diagnostic models expect single timestep inputs and don't handle time windows.

**Workaround:**

1. **Extract single timestep** before passing to diagnostic:
   ```python
   # If input has multiple timesteps, extract latest
   if input.shape[1] > 1:  # time dimension
       input_for_diagnostic = input[:, -1:, :, :, :]
   ```

2. **Loop over timesteps** manually:
   ```python
   for t in range(num_timesteps):
       diagnostic_output = diagnostic_model(input[:, t:t+1, :, :, :])
   ```

---

## Platform-Specific Issues

### ARM64/Apple Silicon Compatibility

**Issue:** Limited support for Apple Silicon Macs  
**Status:** Limited testing on ARM64  
**Affected Platforms:** M1/M2/M3 Macs

**Workaround:**

1. **Use Rosetta 2** for x86 emulation:
   ```bash
   arch -x86_64 pip install earth2studio
   ```

2. **Use Docker** with x86 emulation:
   ```bash
   docker run --platform linux/amd64 ...
   ```

3. **MPS backend** may not work for all models:
   ```python
   # Force CPU execution if MPS fails
   import torch
   torch.backends.mps.enabled = False
   device = torch.device("cpu")
   ```

---

### Windows-Specific Path Issues

**Issue:** Long paths or special characters cause issues on Windows  
**Status:** OS limitation  
**Affected Platforms:** Windows 10/11

**Workaround:**

1. **Enable long paths** in Windows:
   ```
   Computer Configuration → Administrative Templates → 
   System → Filesystem → Enable Win32 long paths
   ```

2. **Use short cache paths**:
   ```bash
   set EARTH2STUDIO_CACHE=C:\e2s
   ```

3. **Use WSL2** for better compatibility:
   ```bash
   wsl --install
   # Install Earth2Studio in WSL2 environment
   ```

---

## Installation Issues

### ONNX Runtime CUDA Binding Errors

**Issue:** ONNX models fail with CUDA binding errors  
**Status:** Installation issue  
**Affected Models:** FengWu, FuXi, Pangu

See [Troubleshooting Guide](docs/userguide/support/troubleshooting.md#onnx-runtime-error-when-binding-input) for solutions.

---

### Flash Attention Long Build Times

**Issue:** Flash Attention takes hours to compile  
**Status:** Known upstream issue  
**Affected Models:** AIFS, AIFSENS

See [Troubleshooting Guide](docs/userguide/support/troubleshooting.md#flash-attention-has-long-build-time-for-aifs-models) for solutions.

---

## Performance Issues

### Slow Data Loading from Cloud Sources

**Issue:** First-time data fetch from cloud sources (ARCO-ERA5, WeatherBench2) is slow  
**Status:** Expected behavior  
**Impact:** Initial runs may take significantly longer

**Workaround:**

1. **Pre-download data**:
   ```python
   # Download data once, cache locally
   data = ARCO_ERA5()
   data(time, variables)  # Caches for future use
   ```

2. **Use local data sources** when possible:
   ```python
   from earth2studio.data import DataArrayFile
   data = DataArrayFile("./local_data.nc")
   ```

3. **Configure cache location** on fast storage:
   ```bash
   export EARTH2STUDIO_CACHE=/path/to/fast/ssd
   ```

---

## Best Practices to Avoid Issues

1. **Always test on small datasets first** before full production runs
2. **Monitor GPU memory** using `torch.cuda.memory_allocated()`
3. **Use FP16 by default** on GPUs with <24GB memory
4. **Clear CUDA cache** between large operations: `torch.cuda.empty_cache()`
5. **Validate dimensions** after data loading and preprocessing
6. **Use Zarr backend** for better I/O performance and reliability
7. **Keep dependencies updated** within compatible version ranges
8. **Check upstream issues** before reporting duplicates

---

## Reporting New Issues

When reporting issues, please include:

1. **System Information:**
   - OS and version
   - Python version
   - PyTorch version
   - CUDA version (if using GPU)
   - GPU model and memory

2. **Earth2Studio Information:**
   - Version: `python -c "import earth2studio; print(earth2studio.__version__)"`
   - Installation method (pip, uv, source)
   - Extras installed

3. **Reproduction Steps:**
   - Minimal code to reproduce
   - Data sources used
   - Error messages (full traceback)

4. **Expected vs Actual Behavior**

5. **Workarounds Attempted**

---

## Contributing Fixes

If you develop a workaround or fix for any issue:

1. Test thoroughly in your environment
2. Document the solution
3. Consider contributing back to upstream
4. Update this document via pull request

---

## Resources

- [Upstream Issue Tracker](https://github.com/NVIDIA/earth2studio/issues)
- [Troubleshooting Guide](docs/userguide/support/troubleshooting.md)
- [FAQ](docs/userguide/support/faq.md)
- [Community Discussions](https://github.com/NVIDIA/earth2studio/discussions)

For urgent issues affecting production systems, consider:
- NVIDIA Enterprise Support (if applicable)
- Commercial support options
- Community help via GitHub Discussions
