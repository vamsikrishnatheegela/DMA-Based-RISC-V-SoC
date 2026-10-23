# Architecture and DMA Transfer Sequence

## Functional blocks

### RISC-V CPU
Initializes data as required by the test sequence and configures the DMA controller with transfer parameters. CPU access to the memory interface resumes when the DMA is inactive.

### Memory-mapped configuration
The CPU accesses DMA control registers using normal address-based read/write transactions. The configuration path should be treated separately from the transfer data path. Exact register offsets and encodings must be obtained from the implementation RTL.

### DMA controller
The DMA controller is an FSM with the documented READ, WAIT, CAPTURE, and WRITE sequence.

| State | Purpose |
|---|---|
| READ | Present the source address and initiate the memory read |
| WAIT | Allow for synchronous RAM read latency |
| CAPTURE | Capture the returned read data |
| WRITE | Write the captured data to the destination |

The source/destination pointers and transfer count are updated as required by the implementation, and completion is indicated by `dma_done`.

### Shared synchronous memory and selection logic
The CPU and DMA share a memory interface. A multiplexer selects address, data, and control signals for the memory. The reported fixed-priority policy selects DMA while `dma_busy` is asserted and returns control to the CPU when the DMA becomes idle.

## End-to-end flow

1. CPU initializes source data.
2. CPU configures DMA source address, destination address, and length through memory-mapped registers.
3. CPU starts the transfer.
4. DMA asserts `dma_busy` and owns the shared memory interface.
5. DMA executes READ → WAIT → CAPTURE → WRITE.
6. DMA updates pointers/count and repeats until the transfer completes.
7. `dma_busy` is deasserted and `dma_done` indicates completion.

## Design trade-off

Fixed-priority selection is straightforward and deterministic, but it does not guarantee fairness when DMA requests remain active or recur back-to-back. Round-robin arbitration, aging, and quantitative starvation analysis are future-work directions.

## Implementation boundary

This document describes the architecture and behavior reported in the paper. Use the original RTL to confirm exact ports, active levels, register map, state encodings, and memory timing.
