# Zynq-7020 Hardware-Software Co-Design AI Accelerator

An end-to-end hardware-software co-design project implementing a quantized AI inference accelerator on the **Xilinx Zynq-7020 SoC FPGA (XC7Z020)**.

## System Architecture
- **Processing System (PS):** Dual-core ARM Cortex-A9 managing host Ethernet/UART communication, DDR3 frame/data buffering, and AXI-Lite control registers.
- **Interconnect:** High-throughput DMA streaming via `S_AXI_HP0` and Xilinx AXI DMA (`MM2S` / `S2MM` channels).
- **Programmable Logic (PL):** Custom parameterized compute engine leveraging Artix-7 `DSP48E1` slices and dual-port `BRAM` ping-pong buffers.

## Repository Structure
- `model/` — PyTorch golden model training, fixed-point quantization (`INT8`/`Q6.10`), and test vector generation.
- `hw/` — SystemVerilog RTL cores, testbenches, `.xdc` constraints, and Vivado Tcl recreation scripts.
- `sw/` — ARM Cortex-A9 C/C++ DMA drivers and host-side Hardware-in-the-Loop (HIL) streaming scripts.
- `docs/` — Architectural diagrams, latency benchmarks, and resource utilization reports.

## Rebuilding the Vivado Project
To recreate the Vivado project and block design from source:
```bash
cd hw/work
vivado -mode batch -source ../scripts/create_project.tcl
