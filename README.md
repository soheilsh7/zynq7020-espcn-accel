# Zynq-7020 ESPCN Super-Resolution Accelerator

Hardware-software co-design project targeting the Xilinx Zynq-7020 (XC7Z020).
The planned ESPCN pipeline upscales a 64×64 luminance tile to 128×128;
software handles chrominance interpolation and RGB reconstruction.

## System architecture

- **PS:** ARM Cortex-A9 manages frame buffers, DMA, and accelerator control.
- **Interconnect:** AXI DMA accesses DDR through `S_AXI_HP0`; AXI-Lite provides accelerator control.
- **PL:** Three convolution layers and 2× PixelShuffle, using UINT8 activations, INT8 weights, and INT32 accumulation/bias. The guide uses weight scaling by 256 and an 8-bit arithmetic right shift for requantization.

## Repository structure

```text
model/                 Quantization and bit-accurate integer emulator
  test_vectors/        Tracked .mem weights/biases and golden .hex vectors
hw/
  rtl/                 Synthesizable RTL
  tb/                  Self-checking testbenches
  scripts/             Vivado project and block-design Tcl scripts
  constraints/         Board-specific XDC constraints
  hls/                 Optional HLS experiments
  work/                Ignored Vivado build workspace
sw/
  baremetal/           ARM DMA drivers and verification applications
  host_stream/         Host image/video streaming client
  linux_drv/           Optional Linux integration
docs/                  Architecture notes and measured results
```

Source directories contain `.gitkeep` placeholders so fresh clones preserve
the scaffold. Build products, environments, and floating-point checkpoints
are ignored; golden vectors remain trackable.

## Python environment

Use Python 3.10 or newer with compatible PyTorch/TorchVision wheels:

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
mkdir -p hw/work
```

`requirements.txt` uses the official PyTorch CPU wheel index. If the system
Python lacks `ensurepip` but has pip installed, bootstrap the environment with:

```bash
python3 -m venv --without-pip .venv
python3 -m pip --python .venv/bin/python install pip --index-url https://pypi.org/simple
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Verify the environment:

```bash
python -c 'import torch, torchvision, numpy, PIL; print(torch.__version__, torchvision.__version__, numpy.__version__, PIL.__version__)'
```

Environment activation applies to the current shell; repeat the `source`
command when opening a new terminal.

## Implementation status

Phase 0 directory scaffolding and ignore rules are in place. A local `.venv`
with pip has been created. Installing and verifying PyTorch, TorchVision,
NumPy, and Pillow is still pending; Phase 0 is not complete until that succeeds.
Phase 1 will add the golden model, quantization scripts, and test vectors.
RTL, drivers, board constraints, and Vivado Tcl scripts are not implemented yet.
The future project recreation script will live at `hw/scripts/create_project.tcl`
and generate its outputs under `hw/work/`.
