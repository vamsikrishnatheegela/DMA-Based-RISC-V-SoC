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

## 1. Why this project?

Modern SoCs move data between memory locations and hardware blocks while also executing application instructions. With a CPU-managed transfer, the processor repeatedly issues or controls individual memory operations, using execution time that could otherwise be spent on computation.

A DMA controller moves this responsibility into dedicated hardware. The CPU programs the transfer, and the DMA controller performs the data-movement sequence autonomously. This can reduce the processor's involvement in the active transfer window, although the actual performance and power benefit depends on the implementation and workload.

DMA introduces a shared-resource problem: the CPU and DMA controller can both request access to the same memory interface. The system therefore needs defined ownership selection to avoid conflicting control of the shared memory signals.

This project explores that integration problem: how to combine a RISC-V processor, a DMA transfer engine, synchronous memory, and a simple priority policy, then carry the design through simulation and physical implementation.

## 2. System architecture

The design is organized into four main functional elements.

### RISC-V CPU — configuration and system control

The processor initializes memory as required by the test sequence and configures the DMA controller. The transfer parameters described in the project include the source address, destination address, and transfer length.

### Memory-mapped DMA configuration

A memory-mapped interface allows the CPU to access DMA control registers using address-based read/write operations. These control registers are conceptually distinct from the shared data RAM: the CPU uses the interface to configure the controller, whereas the DMA accesses the shared memory to move data. Exact register addresses and bit encodings should be taken from the RTL implementation rather than inferred from this overview.

### FSM-based DMA controller — transfer execution

After configuration and activation, the DMA controller sequences the read and write operations. It generates addresses, captures read data, updates address pointers and the transfer count, and indicates completion.

The documented sequence is:

1. **READ** — present the source address and initiate a read.
2. **WAIT** — accommodate the synchronous memory's read latency.
3. **CAPTURE** — capture the returned data.
4. **WRITE** — write the captured data to the destination address.

The sequence repeats for the programmed transfer until the completion condition is reached.

### Shared synchronous RAM and memory-interface selection

The CPU and DMA share a synchronous memory interface. Multiplexer-based selection logic chooses which master's address, data, and control signals are routed to the memory interface.

The documented ownership rule is fixed priority:

- When `dma_busy` is asserted, the DMA controls the shared memory interface.
- When the DMA becomes idle and `dma_busy` is deasserted, CPU memory access resumes.

This is **fixed-priority selection implemented with multiplexer-based logic**, not a round-robin arbiter or a separate sophisticated arbitration protocol.

## 3. Design and verification flow

The project describes the following tool flow:

1. **Verilog RTL** — CPU integration, DMA FSM, synchronous-memory interface, and memory-selection logic.
2. **Cadence Xcelium** — functional simulation of the initialization, configuration, transfer, and completion sequence.
3. **Cadence Genus** — synthesis, technology mapping to standard cells, and constrained timing analysis.
4. **Cadence Innovus** — physical implementation stages, including placement, clock-tree synthesis, routing, and the reported physical/timing results.

The target clock constraint is 100 MHz, equivalent to a 10 ns period. A target constraint is not itself a measurement of maximum operating frequency. The reported study does not claim a maximum frequency based on a systematic frequency sweep.

## 4. Functional simulation

The simulation narrative reported for the project is:

1. The CPU initializes memory with test data, including the hexadecimal patterns `DEADBEEF` and `CAFEBABE`.
2. The CPU writes the DMA configuration through its memory-mapped interface and initiates a transfer.
3. The DMA asserts `dma_busy` and takes control of the shared memory interface.
4. The DMA executes the READ, WAIT, CAPTURE, and WRITE sequence while source/destination address pointers and transfer count progress.
5. At completion, `dma_busy` goes low and `dma_done` is asserted.

The waveform is used to inspect clocked control behavior, bus values, transfer progression, and the CPU-to-DMA handoff. The WAIT state is important because the RAM read data follows synchronous timing; data must be captured at the correct time.

> **Verification boundary:** The described waveform supports the intended functional sequence. It does not, by itself, prove a numerical speedup over CPU-managed transfers, a power advantage, a maximum operating frequency, or quantitative arbitration fairness.

## 5. Reported implementation results

| Metric | Reported result | Interpretation |
|---|---:|---|
| Standard-cell area | 1889.222 µm² | Cell-area metric; not the complete die/core area |
| Total power | 0.13658 mW | Must be interpreted using the workload, activity source, and analysis conditions |
| Setup WNS | +5.595 ns | Positive setup slack under the applied timing constraints |
| Setup TNS | 0 ns | No accumulated negative setup slack in the reported analysis |
| Target clock | 100 MHz (10 ns) | Applied design constraint, not a measured maximum frequency |
| Placement density | approximately 72.932% | Reported placement-utilization metric |
| Horizontal congestion overflow | 0% | Reported congestion result |
| Vertical congestion overflow | 0% | Reported congestion result |
| Final setup violations | 0 | As reported for the final implementation |
| Final hold violations | 0 | As reported for the final implementation |
| Routed wire length | 3174 µm | Reported routing statistic |
| Vias | more than 2000 | Reported routing statistic |

### How to interpret these numbers responsibly

- **Area scope matters.** The standard-cell area should not be equated with the total core or die area. Before making cross-design comparisons, identify which modules and memory structures are included in the netlist and area report, and whether memory is implemented as standard cells or a macro.
- **Power is workload-dependent.** The project reports 0.13658 mW and describes power as involving leakage, internal, switching, and clock components for a DMA workload. A meaningful comparison needs the exact activity source, operating conditions, and included blocks.
- **Timing is constraint-dependent.** The reported positive WNS applies to the analyzed paths and timing constraints. Since no systematic frequency sweep is reported, do not present 100 MHz as the maximum achievable frequency.
- **Congestion is not DRC.** Zero routing-congestion overflow does not establish that every design-rule check passed. Claim DRC-clean status only when a corresponding DRC report is available.
- **Small-area results require context.** Verify the actual synthesis report to establish whether the CPU and RAM are included, simplified, represented by macros, or excluded. This repository deliberately does not speculate about that scope.

## 6. CPU-managed transfer versus DMA-managed transfer

The project provides a **qualitative architectural comparison**, not a measured numerical benchmark.

| Aspect | CPU-managed transfer | DMA-managed transfer in this project |
|---|---|---|
| Transfer control | CPU manages individual memory operations | CPU configures; DMA executes the sequence |
| Active-transfer processor involvement | CPU remains involved in data movement | DMA performs the sequence autonomously |
| Shared memory access | CPU issues requests | Multiplexer selects DMA while `dma_busy` is asserted |
| Numerical execution-time comparison | Not measured | Not measured |
| Numerical power comparison | Not measured | Not measured |

No direct numerical CPU-versus-DMA execution-time or power comparison is reported in this study. Consequently, this work does not claim a quantified speedup, percentage energy saving, or numerical throughput improvement.

A useful extension would be to implement a CPU-managed copy baseline and compare both methods under the same memory configuration, clock constraint, transferred data size, and activity/power-analysis methodology.

## 7. Limitations and future work

The present implementation leaves several opportunities for extension:

- **Burst transfers:** reduce per-element control overhead and investigate throughput.
- **Fairness-aware arbitration:** compare fixed priority with round-robin or aging policies and quantify CPU waiting time.
- **Standard interconnect:** investigate AXI integration and the associated protocol/control overhead.
- **Frequency sweep:** repeat timing analysis at different clock periods to characterize timing margin and investigate the limiting paths.
- **Quantitative baseline:** compare CPU-managed and DMA-managed transfers using cycle counts, CPU busy time, and power under matching conditions.
- **Reproducibility:** publish the precise RTL, testbench, constraints, tool scripts, configuration, and reports where permission allows.

## 8. Artifacts and repository organization

This repository is intended to make the architecture and results understandable without requiring readers to infer missing implementation details.

Suggested artifact organization (include each item only when the actual file is available):

| Directory | Intended contents |
|---|---|
| `rtl/` | Original Verilog modules: CPU/top level, DMA controller, memory model/interface, arbitration logic |
| `testbench/` | Actual simulation testbench and stimuli |
| `docs/` | Conference paper, architecture notes, signal descriptions, design decisions |
| `images/` | Architecture diagram, simulation waveform, schematics, routed-layout screenshots |
| `reports/` | Actual synthesis, timing, power, congestion, and physical verification reports |
| `scripts/` | Reproducible simulation, synthesis, and implementation scripts |
| `references.md` | Complete bibliographic details for cited sources |

Cadence databases, technology libraries, PDK files, licensed models, and other proprietary or restricted artifacts should not be committed unless their redistribution is permitted. Screenshots and summarized reports can be used where publication is authorized.

## 9. Tools and technologies

- Verilog HDL
- RISC-V CPU architecture
- Direct Memory Access (DMA)
- Finite-state-machine (FSM) control
- Synchronous RAM
- Multiplexer-based fixed-priority memory selection
- Cadence Xcelium
- Cadence Genus
- Cadence Innovus
- 90 nm technology flow

Exact tool versions, library names, PVT corners, SDC constraints, and memory configuration should be documented from the actual run setup. They are not inferred here.

## 10. References

The paper's bibliography should be preserved and completed with exact publication details for the version used. Relevant background sources include:

1. David A. Patterson and John L. Hennessy, *Computer Organization and Design RISC-V Edition: The Hardware/Software Interface*, 2nd edition, Morgan Kaufmann, 2020.
2. David A. Patterson and Andrew Waterman, *The RISC-V Reader: An Open Architecture Atlas*, Strawberry Canyon, 2017.
3. Andrew Kidwell, M. P. Horvath Jr., D. Hanna, and B. Jones, “Lightweight Direct Memory Access on FPGA Using AXI Protocol,” 2023 Congress in Computer Science, Computer Engineering, and Applied Computing, 2023.
4. David M. Harris and Sarah L. Harris, *Digital Design and Computer Architecture*, 2nd edition, Morgan Kaufmann, 2012.

For any formal publication, verify the complete citation details and add the exact RISC-V specification and Cadence documentation versions used.

## Authors and affiliation

**Authors listed in the conference paper:** Theegela Vamsi Krishna, Arnav Nagar, Ayan Arnav Ghosh, Pramod Martha, Adarsh Nigam, and Amit Kumar Goyal.

**Affiliation:** Manipal Institute of Technology, Manipal Academy of Higher Education, Manipal, India.

## Project status

This README describes the reported architecture and evaluation. The repository should be considered fully reproducible only after the original RTL, testbench, constraints, scripts, and shareable tool reports are added and checked. No missing implementation files or measurements are fabricated by this documentation.
