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

## Source inspection — 2026-10-16

Verified in `OptiScaler/dlssnr/design/pre-sr-multipass.md`:

- The multipass answer already ping-pongs between two persistent same-size resources (`base -> A -> B -> A`). Do **not** propose a second ping-pong allocation as an optimization.
- Pre-SR Color lacking UAV capability is composed into an owned scratch texture and copied back. This copy cannot simply be removed unless the destination can be written legally and state transitions are preserved.
- Origin-zero padded/max-sized Color resources are compacted to an active-size UAV resource before processing; the active rectangle is then copied back. These copies preserve padding and are counted in the NR GPU timing interval.
- Unsupported offsets/layouts fall back post-SR. A candidate fast path must preserve that fallback and deferred resource retirement.

**Candidate experiment:** add read-only diagnostic counters/timestamps around the compact-color input/output copy path, split the reported NR interval into copy and non-copy GPU work where valid, then test whether the compact-color allocation/copy can be skipped *only* for already-active-sized, UAV-compatible Color resources. Confirm whether that case already bypasses copies before adding any optimization.

**Important:** the design notes establish the copy rationale, but this inspection has not yet identified the implementation's exact C++ call sites. No redundant copy has been proven, so changing rendering code now would be speculative.
