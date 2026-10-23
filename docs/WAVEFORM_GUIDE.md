# Simulation Waveform Guide

The paper describes a Cadence Xcelium functional simulation of a DMA-based RISC-V SoC. The available narrative identifies the following sequence.

## Expected phases

1. **Initialization:** CPU-side memory transactions initialize data, including the reported hexadecimal patterns `DEADBEEF` and `CAFEBABE`.
2. **Configuration:** CPU writes DMA configuration parameters through the memory-mapped interface.
3. **Activation:** `dma_busy` asserts; multiplexer-based selection gives the DMA control of the shared memory interface.
4. **Read:** DMA presents the source address.
5. **Wait:** the FSM accommodates synchronous memory's read latency.
6. **Capture:** valid returned data is captured.
7. **Write:** DMA writes data to the destination address.
8. **Progression:** source/destination pointers and transfer count update as the controller processes the programmed transfer.
9. **Completion:** `dma_busy` deasserts and `dma_done` is asserted.

## How to read the waveform

- The horizontal axis is simulation time.
- The clock provides the timing reference for sequential operations.
- Single-bit control signals show logical states and events.
- Multi-bit address/data buses show values changing over time.
- Address activity together with write control and data signals can be used to inspect the read/write sequence.
- The busy and done signals mark the active-transfer interval and the completion event.

## Cautions

Exact signal names, transitions, values, and durations must be checked in the original high-resolution waveform or simulator database. The project screenshots are not sufficient to confidently transcribe all bus values. This guide does not invent cycle counts or transfer-latency numbers.

A waveform demonstrates functional sequencing; it does not alone demonstrate a numerical speedup, power improvement, maximum frequency, or arbitration fairness.
