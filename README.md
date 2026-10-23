# Design and Implementation of a DMA-Based RISC-V SoC with Priority Bus Arbitration

A System-on-Chip (SoC) design study integrating a RISC-V CPU, an FSM-based Direct Memory Access (DMA) controller, shared synchronous memory, and fixed-priority memory-interface selection. The work covers RTL-level behavior, functional simulation, synthesis, timing analysis, and physical implementation using Cadence tools in a 90 nm technology flow.

> **Project scope:** This repository documents the architecture and the results reported for the project. RTL source files, tool scripts, raw reports, and waveforms should be added only when the corresponding original artifacts are available and approved for publication. The numerical results below are reported project results; see the interpretation notes before comparing them with other designs.

## At a glance

| Item | Reported project detail |
|---|---|
| Project | DMA-Based RISC-V SoC with Priority Bus Arbitration |
| Architecture | RISC-V CPU + FSM-based DMA + shared synchronous RAM |
| Transfer type | Memory-to-memory |
| Memory access selection | Fixed-priority DMA-over-CPU using multiplexer-based logic |
| DMA control sequence | READ → WAIT → CAPTURE → WRITE |
| RTL language | Verilog HDL |
| Functional simulation | Cadence Xcelium |
| Logic synthesis | Cadence Genus |
| Physical implementation | Cadence Innovus |
| Technology flow | 90 nm |
| Target clock | 100 MHz (10 ns period) |
| Reported standard-cell area | 1889.222 µm² |
| Reported total power | 0.13658 mW |
| Reported setup WNS | +5.595 ns |
| Reported setup TNS | 0 ns |
| Reported placement density | approximately 72.932% |

## Project overview

The design addresses the control overhead associated with CPU-managed data movement. The CPU initializes/configures a transfer; a dedicated DMA finite-state machine then sequences memory reads and writes without requiring the CPU to control each individual movement operation.

DMA introduces a shared-resource problem because the CPU and DMA use a common memory interface. The design applies fixed-priority selection: DMA controls the shared memory while `dma_busy` is asserted, and CPU memory access resumes when the DMA becomes idle.

## System architecture

- **RISC-V CPU:** initializes data as required by the test sequence and configures source address, destination address, and transfer length.
- **Memory-mapped configuration:** allows CPU read/write transactions to address DMA control registers. These control registers are conceptually distinct from the shared data RAM. Exact register offsets and encodings must be confirmed from the RTL.
- **FSM-based DMA controller:** generates addresses, sequences reads and writes, updates pointers/count, and indicates completion.
- **Shared synchronous RAM:** stores the source and destination data.
- **Multiplexer-based memory selection:** selects CPU or DMA control signals for the shared memory interface. The documented fixed-priority rule selects DMA while `dma_busy` is asserted.

### DMA state sequence

1. **READ** — present the source address and initiate a memory read.
2. **WAIT** — accommodate synchronous RAM read latency.
3. **CAPTURE** — capture the returned data.
4. **WRITE** — write the captured data to the destination address.

The sequence repeats as necessary until the programmed transfer completes. The completion behavior reported for the simulation is that `dma_busy` goes low and `dma_done` is asserted.

## Design and verification flow

`Verilog RTL → Cadence Xcelium functional simulation → Cadence Genus synthesis → Cadence Innovus physical implementation`

The described physical flow includes placement, clock-tree synthesis, routing, and the reported implementation checks. The target clock is 100 MHz (10 ns). This is a target constraint, **not a measured maximum operating frequency**.

## Functional simulation

The simulation narrative records:

1. CPU initialization using test values that include `DEADBEEF` and `CAFEBABE`.
2. CPU configuration and activation of the DMA.
3. DMA ownership of the shared memory interface while `dma_busy` is asserted.
4. READ/WAIT/CAPTURE/WRITE operations with changing addresses as the transfer progresses.
5. Completion indicated by `dma_busy` deasserting and `dma_done` asserting.

The waveform is functional evidence of sequencing and memory-interface handoff. It does not by itself measure CPU-vs-DMA speedup, power advantage, maximum frequency, or arbitration fairness.

## Reported implementation results

| Metric | Reported result |
|---|---:|
| Technology flow | 90 nm |
| Target clock | 100 MHz (10 ns) |
| Standard-cell area | 1889.222 µm² |
| Total power | 0.13658 mW |
| Setup WNS | +5.595 ns |
| Setup TNS | 0 ns |
| Placement density | approximately 72.932% |
| Routed wire length | 3174 µm |
| Vias | more than 2000 |
| Horizontal congestion overflow | 0% |
| Vertical congestion overflow | 0% |
| Final setup violations | 0 |
| Final hold violations | 0 |

### Interpretation and scope

- **Area:** 1889.222 µm² is the reported standard-cell area, not total die/core area. Before comparing with other SoCs, establish exactly which blocks and memory structures the report includes and whether RAM is implemented as standard cells or a macro. Do not assume that CPU or RAM was blackboxed or excluded without checking the report.
- **Power:** 0.13658 mW is workload- and analysis-condition-dependent. The workload, activity source, models, operating conditions, and included blocks matter.
- **Timing:** positive WNS and zero TNS are tied to the applied constraints. No systematic frequency sweep is reported, so maximum frequency is unknown.
- **Congestion versus DRC:** zero congestion overflow is not proof of a DRC-clean layout. Only claim zero DRC violations if a corresponding verification report supports it.
- **Performance comparison:** this work provides a qualitative architectural comparison; it does not report a numerical CPU-based versus DMA-based execution-time or power baseline. No measured speedup or percentage energy saving is claimed.

## Where this architecture is useful

DMA-based data movement is relevant to many classes of embedded and SoC systems:

- **Embedded systems:** moving buffers between memory and peripheral-facing logic.
- **Sensor and data acquisition:** transferring sampled data while reducing processor involvement in each movement.
- **Communication subsystems:** moving packet/frame buffers between memory and interfaces.
- **Audio/video pipelines:** moving stream buffers between processing stages.
- **Hardware accelerators:** moving accelerator input/output data while the CPU handles configuration.
- **Computer architecture education:** demonstrating memory-mapped registers, FSM design, synchronous-memory latency, resource sharing, synthesis, timing, and physical implementation.

These are general application contexts for DMA-based architectures, not measured use cases demonstrated by the present design.

## Limitations and future work

- Implement burst-transfer support.
- Compare fixed-priority arbitration with round-robin or aging-based policies and quantify CPU wait time.
- Investigate a standard interconnect such as AXI.
- Implement a CPU-managed copy baseline and compare cycle count, CPU busy time, and power under matched conditions.
- Sweep clock constraints to investigate timing margin and critical paths.
- Publish original RTL, testbench, constraints, tool scripts, and permitted raw reports for reproducibility.

## Repository contents

- `docs/ARCHITECTURE.md` — functional blocks, state sequence, and memory ownership.
- `docs/PROJECT_OVERVIEW.md` — motivation, applications, trade-offs, and extensions.
- `docs/RESULTS.md` — numeric results with interpretation and caveats.
- `docs/WAVEFORM_GUIDE.md` — how to read the reported simulation sequence.
- `docs/PROJECT_STATUS.md` — artifacts present and still needed for reproducibility.
- `references.md` — selected sources to be verified against the paper's bibliography.
- `images/` — intended location for approved architecture, waveform, schematic, flow, and layout figures.

**Important:** the original RTL/testbench, constraints/scripts, tool reports, conference PDF, presentation deck, and original binary figures still need to be added from the project workspace. A screenshot is evidence for review and presentation, but is not a substitute for a reproducible simulation or implementation flow.

## Tools and technologies

Verilog HDL · RISC-V · DMA · FSM control · synchronous RAM · fixed-priority memory selection · Cadence Xcelium · Cadence Genus · Cadence Innovus · 90 nm technology flow.

Exact tool versions, library names, PVT corners, SDC constraints, and memory configuration should be documented from the original run setup rather than inferred.

## References

Please verify complete bibliographic details against the sources actually cited in the paper before formal publication.

1. D. A. Patterson and J. L. Hennessy, *Computer Organization and Design RISC-V Edition: The Hardware/Software Interface*, 2nd ed., Morgan Kaufmann, 2020.
2. D. A. Patterson and A. Waterman, *The RISC-V Reader: An Open Architecture Atlas*, Strawberry Canyon, 2017.
3. A. Kidwell, M. P. Horvath Jr., D. Hanna, and B. Jones, “Lightweight Direct Memory Access on FPGA Using AXI Protocol,” 2023 Congress in Computer Science, Computer Engineering, and Applied Computing, 2023. Verify complete proceedings details/DOI.
4. D. M. Harris and S. L. Harris, *Digital Design and Computer Architecture*, 2nd ed., Morgan Kaufmann, 2012.
5. Add the exact RISC-V ISA specification edition used and the exact Cadence documentation titles/versions available to the project.

## Authors and affiliation

**Authors listed in the conference paper:** Theegela Vamsi Krishna, Arnav Nagar, Ayan Arnav Ghosh, Pramod Martha, Adarsh Nigam, and Amit Kumar Goyal.

**Affiliation:** Manipal Institute of Technology, Manipal Academy of Higher Education, Manipal, India.

## Publication and reproducibility notes

Confirm that all authors and the institution permit public distribution of the paper, slide deck, screenshots, and tool outputs. Do not commit proprietary PDKs, standard-cell libraries, licensed simulation models, credentials, private paths, or restricted Cadence databases.

This README describes the reported architecture and reported values. Consider the project fully reproducible only after the original RTL, testbench, constraints, scripts, and shareable tool reports are added and checked.
