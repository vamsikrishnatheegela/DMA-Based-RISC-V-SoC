# Reported Results and Interpretation

This page records the values reported in the project paper/presentation. Confirm each metric against the final tool report before publishing a claim about reproducibility or design scope.

| Metric | Reported value |
|---|---:|
| Technology flow | 90 nm |
| Target clock | 100 MHz |
| Clock period | 10 ns |
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

## Functional verification sequence

The reported simulation sequence comprises CPU memory initialization (test patterns include `DEADBEEF` and `CAFEBABE`), CPU DMA configuration via a memory-mapped interface, DMA activation, READ/WAIT/CAPTURE/WRITE operations, address progression, and completion indicated by `dma_busy` going low and `dma_done` being asserted.

## Interpretation notes

### Standard-cell area
The reported 1889.222 µm² is a standard-cell area value and should not be described as complete die/core area. Verify whether CPU and RAM logic are included in the area report and how memory is represented before making comparisons. Do not assume blackboxing or exclusion without evidence.

### Power
The reported 0.13658 mW is tied to the workload, activity data, models, and analysis conditions. For a reproducible result, record whether switching activity is RTL- or gate-level, the stimulus/workload, voltage, temperature, library corners, and exactly which blocks were included.

### Timing
The positive WNS and zero TNS are results under the applied constraints. The 100 MHz setting is the target clock, not a determined maximum operating frequency. The study did not perform a systematic frequency sweep.

### Physical verification
Zero congestion overflow is not equivalent to zero DRC violations. Report DRC status only if the actual DRC report supports it.

### Comparison limitations
No direct numerical CPU-vs-DMA execution-time or power baseline is reported. Do not claim measured speedup, throughput improvement, or percentage energy savings.

## Useful additional experiments

- Compare CPU-managed and DMA-managed transfers using identical data sizes and memory timing.
- Record clock cycles from a consistent start event to completion.
- Measure CPU active/busy cycles during each transfer method.
- Run power analysis with comparable activity sources and operating conditions.
- Perform timing analysis across a clock-period sweep and record the limiting path.
- Quantify CPU waiting time under repeated DMA requests and compare arbitration policies.
