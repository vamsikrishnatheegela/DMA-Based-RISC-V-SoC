# Project Overview: Engineering Context and Applications

## What problem is addressed?

Data movement is a regular operation in digital systems. In a CPU-managed copy, the processor participates in issuing the sequence of memory operations. A DMA controller moves the transfer sequencing into dedicated hardware so the CPU can configure a transfer and then avoid managing every individual movement operation.

The design explored in this project integrates that idea with a RISC-V CPU and shared synchronous RAM. Because the CPU and DMA controller use the same memory interface, a defined ownership rule is necessary. This implementation uses fixed-priority DMA-over-CPU selection through a multiplexer.

## Where this architecture is useful

DMA-based data movement is a common architectural technique in systems where a processor and peripherals or hardware blocks exchange data. Examples of application areas include:

- **Embedded systems:** moving buffers between memory and peripheral-facing logic.
- **Sensor and data-acquisition systems:** transferring batches of sampled data while reducing processor involvement in each movement.
- **Communication subsystems:** moving packet or frame buffers between memory and interfaces.
- **Audio/video pipelines:** moving stream buffers between processing stages.
- **Hardware accelerators:** feeding input data and collecting output data while a CPU manages configuration and control.
- **SoC and computer-architecture education:** demonstrating CPU control, memory-mapped registers, FSM design, synchronous-memory latency, resource sharing, synthesis, and physical implementation in one integrated project.

These are general application contexts for DMA-based architectures, not claims that the current implementation contains or has been validated with all of these peripherals or workloads.

## What was implemented in this project?

1. A SoC structure containing a RISC-V CPU, FSM-based DMA controller, and shared synchronous memory.
2. Memory-mapped configuration of the DMA transfer.
3. A READ → WAIT → CAPTURE → WRITE transfer-control sequence to accommodate synchronous RAM timing.
4. Multiplexer-based memory-interface selection that prioritizes DMA while `dma_busy` is active.
5. Functional verification of initialization, DMA activation, transfer progression, and completion.
6. Reported synthesis and physical-design metrics from Cadence Genus and Innovus using a 90 nm technology flow.

## Engineering trade-offs

### Deterministic DMA priority
Fixed priority is simple to reason about, but CPU access may be delayed for the duration of an active DMA transfer. Repeated back-to-back transfers can extend that delay. Fairness-aware schemes are possible extensions, but were not implemented in the described design.

### Synchronous memory timing
A synchronous RAM read introduces a clocked delay. The DMA FSM must not capture invalid or stale read data; the WAIT/CAPTURE stages address this control requirement.

### Measurement and reproducibility
A strong evaluation of a DMA design should record the exact RTL scope, memory implementation, constraints, workload, activity source, and analysis conditions. This project reports area, power, and timing values, but does not provide a numerical CPU-only performance baseline or a systematic frequency sweep.

## Potential extensions

- Add burst-transfer functionality.
- Compare fixed-priority arbitration against round-robin and aging-based policies.
- Integrate an industry-standard bus protocol such as AXI.
- Build a matched CPU-managed-copy baseline and compare cycle count, CPU busy time, throughput, and power.
- Sweep clock constraints and identify the critical timing paths.
- Publish the original RTL, verification environment, SDC and tool scripts, and permitted implementation reports.

## Scope statement

The application areas above describe where this general architectural approach can be relevant. They are not measured demonstrations of the current project's performance in a specific product or workload. Reported numeric values should be interpreted only within the scope and conditions of the actual tool reports.
