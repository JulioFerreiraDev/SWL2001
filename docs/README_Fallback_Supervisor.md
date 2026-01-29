# Rate-lock progressive fallback supervisor (SWL2001 v4.8.0)

This repository contains a change on top of SWL2001 v4.8.0 to implement a **rate-lock progressive fallback supervisor** driven by **confirmed uplink ACK failures**. The goal is to increase robustness in challenging links by temporarily forcing higher SFs in steps, then returning to the default ADR behavior after a fixed period.

## What was changed

- **File:** `lbm_examples/main_examples/main_periodical_uplink.c`  
- **Goal:** when consecutive **confirmed uplinks** do not receive **ACK**, temporarily enable a **progressive fallback** by applying a **rate-lock** (custom ADR profile) that forces the SF/DR in steps (e.g., increasing SF progressively up to a defined maximum). After a fixed number of cycles, the device restores the **default ADR** configuration.

## Flowchart

![Progressive fallback flowchart](figures/flowchart_fallback.png)

## Key variables (as implemented)

- `consecutive_ack_failures`: counts consecutive confirmed uplinks without ACK  
- `progressive_fallback_active`: indicates whether progressive fallback mode is active  
- `progressive_fallback_level`: current fallback step (mapped to a forced SF/DR)  
- `progressive_fallback_usage`: counts cycles spent in progressive fallback mode  
- `last_uplink_confirmed_requested`: indicates whether the last uplink requested confirmation  

## Trigger, progression, and exit criteria

- **Enter fallback** when `consecutive_ack_failures > 5` and `progressive_fallback_active == false`.  
- **While fallback is active**, keep the rate-lock applied and increment `progressive_fallback_usage`.  
- **Progress the fallback** by increasing `progressive_fallback_level` when ACK failures persist, forcing a higher SF in steps up to a defined maximum.  
- **Exit fallback** when `progressive_fallback_usage >= 20`, restoring default ADR and clearing the fallback state.

## How to cite

Use the GitHub Release **rate-lock-progressive-480.1 (SWL2001 v4.8.0)** and the Zenodo DOI: `https://doi.org/10.5281/zenodo.18397028`.
