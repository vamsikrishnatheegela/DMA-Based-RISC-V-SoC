# Artifact and Reproducibility Status

This page distinguishes materials currently documented in the repository from artifacts still needed to reproduce the implementation.

## Present in this repository

- Main README describing the project, architecture, reported results, limitations, and future work.
- Architecture and DMA-sequence notes.
- Reported results and interpretation notes.
- Waveform-reading guide based on the narrative in the paper.
- Selected reference list with items flagged for bibliographic verification.

## Still to add from the original design workspace

| Artifact | Expected content | Status |
|---|---|---|
| RTL | CPU/top, DMA FSM, memory interface/model, mux selection logic | Not yet included |
| Testbench | Original stimulus and checks | Not yet included |
| Xcelium waveform | Original database or high-resolution exported image | Screenshot exists in project conversation; source database not included |
| Genus setup | Constraints, library references, synthesis scripts | Not yet included |
| Innovus setup | Floorplan/placement/CTS/route scripts and config | Not yet included |
| Raw reports | Area, timing, power, congestion, DRC/DRV as applicable | Not yet included |
| Paper PDF | Approved shareable final conference version | Not yet included |
| Presentation | Approved shareable conference slide deck | Not yet included |
| Design figures | Original architecture, RTL schematics, flow, layout exports | Not yet included as repository files |

## Why this distinction matters

A screenshot of a waveform is useful presentation evidence but is not a reproducible testbench. A physical-layout image is useful evidence of implementation but does not substitute for constraints, technology libraries, scripts, or signoff reports.

Add the original files from the project workspace when available. Do not reconstruct or relabel generated RTL, reports, or tool databases as the actual implementation unless they are the original artifacts.

## Publication and licensing

Before publishing a paper, slide deck, screenshots, or tool outputs in a public repository, confirm that all authors and the institution permit public distribution. Do not publish proprietary PDKs, standard-cell libraries, licensed simulation models, credentials, private paths, or restricted Cadence databases.
