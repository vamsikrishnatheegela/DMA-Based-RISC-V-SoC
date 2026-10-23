# Available Project Artifacts

This repository documents the project **Design and Implementation of a DMA-Based RISC-V SoC with Priority Bus Arbitration**.

## Source files available in the working artifact bundle

The following source documents and extracted figures were available during repository preparation:

- Conference paper PDF: `ICCES2026_Vamsi.pdf`
- Conference presentation: `Design-and-Implementation-of-a-DMA-Based-RISC-V-SoC-with-Priority-Bus-Arbitration.pptx`
- SoC architecture diagram
- Top-level schematic
- CPU module schematic
- DMA module schematic
- Innovus layout screenshots, including routed layout
- RTL synthesis flow figure
- Xcelium simulation waveform screenshots

## Important repository status

These files were available in the ChatGPT working environment, but the connected GitHub operations available for this session only support writing UTF-8 text files and Git objects. They do not provide a direct local-workspace binary-file upload operation. Therefore, the PDF, PPTX, and image binaries have **not** been committed by this update. This manifest intentionally does not pretend they are present in the repository.

The original RTL source files, testbenches, and native Cadence reports were not found in the available paper/presentation artifact bundle. They should only be added when the original project files are available; do not reconstruct guessed RTL and present it as the original implementation.

## Extracted figure inventory

The prepared supporting-artifacts archive contains these figure files:

- `images/soc-architecture.png`
- `images/top-level-schematic.png`
- `images/cpu-module-schematic.png`
- `images/dma-module-schematic.png`
- `images/schematic-from-presentation.png`
- `images/innovus-layout-presentation.jpg`
- `images/innovus-layout-uploaded.png`
- `images/innovus-routed-layout.png`
- `images/rtl-synthesis-flow.png`
- `images/xcelium-waveform-latest.png`
- `images/xcelium-waveform-paper.png`
- `images/xcelium-waveform-presentation.jpg`

## Reported implementation results

The conference paper reports the following results; check the original tool reports before treating these as independently reproduced:

| Metric | Reported value |
|---|---:|
| Technology node | 90 nm |
| Target clock period | 10 ns (100 MHz target) |
| Standard-cell area | 1889.222 µm² |
| Power | 0.13658 mW |
| Setup WNS | +5.595 ns |
| Setup TNS | 0 ns |
| Placement density | 72.932% |
| Horizontal/vertical congestion overflow | 0% |
| Routed wire length | 3174 µm |
| Final setup/hold violations | 0 (as reported) |

The 100 MHz value is a design target, not a demonstrated maximum operating frequency. Power depends on the simulation activity and analysis setup. Zero congestion overflow is not, by itself, proof of DRC-clean layout. The scope of the reported area should be verified against the original synthesis reports.
