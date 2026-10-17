# Pre-SR copy optimization — investigation plan

Status: investigation only; no performance improvement has been demonstrated.

## Objective
Find avoidable temporary texture copies in the DLSS-NR pre-SR path while preserving resource states, GPU synchronization, motion vectors, NR output and MFG.

## Audit checklist
1. Locate scratch texture allocations and copy operations in the pre-SR path.
2. Record copy preconditions: formats, dimensions, resource states, barriers, ownership, lifetime, fences and aliasing.
3. Measure copy counts, bytes transferred, VRAM usage and GPU times (average and p99) before changing behavior.
4. Prototype a guarded opt-in fast path only when resource compatibility is proven; otherwise retain original path.
5. Compare against the same Shadow of the Tomb Raider scene/settings on RTX 4050 6GB, with pre-SR, WorkingScale, passes, RT and MFG held constant.
6. Check ghosting, flicker, device removal and crashes. Do not merge without local GPU validation.

## Next step
Identify the concrete implementation files and validate the copy hypothesis. No code changes or benchmarks have been performed yet.
