# Upstream 0.19.10 integration

Base: geodesa/main `27a2684`; upstream: `ec1cac7` (v0.19.10); common ancestor: `53d92ab`. The deployed consumer revision `f20620d` is a divergent lineage, not an ancestor of geodesa/main.

## Preserved consumer APIs

- Ported `f20620d` cuBLASLt MatmulOperation, MatmulPreference, MatmulAlgorithm, algorithm enumeration result wrappers, and ScaledMatmul APIs by three-way merge. Existing APIs are preserved; CUDA 13.2/13.3/13.4 feature gates now expose scaled matmul too.
- Retained geodesa/main explicit MatmulConfigWithComputeType policy alongside those APIs.
- Retained `e2cd22d` allocation-kind tagging and graph capture address lifetime requirements. Newer geodesa shared CaptureAllocationLease ownership and transient allocation reuse supersede the older raw capture pool. Graph-owned drops skip capture-local event waits, and event recording remains suppressed during retained capture.
- Retained `f20620d` zero-length allocation behavior, including pool allocation null slices.
- Retained graph definitions/executables, node updates, raw instantiation, default-kind streams, cuBLAS capture stream redirects, synchronous allocation, cuSOLVER LAPACK wrappers, and cuTENSOR safe APIs.

## Merge resolutions

- Preserve atomic allocation capability and capture pool initialization in upstream non-primary/raw context constructors. Preserve stream ownership in default-kind stream construction.
- Consolidate duplicate upstream/fork memory-pool result modules without changing fork call signatures. Upstream raw helpers are available as create_with_props, get_attribute_raw, set_attribute_raw, and trim_to.
- Handle CUDA 13.2+ CUmemLocation union layout.
- Preserve semantic CUDA version cfg flags and upstream library version selection.
- Use upstream graph instantiation cleanup error tracking and bitmask flags.

## Consumer migration

Package version becomes 0.19.10. CUgraphInstantiate_flags is now a bitmask newtype: construct zero flags with CUgraphInstantiate_flags(0), and use .0 instead of enum casts. CUmemAllocationHandleType also uses a bitmask newtype. Existing broad cudnn/nccl/cutensor features select upstream defaults; version-specific features are available. Active consumers are deliberately not repinned by this source update.

## Validation

Single-job cargo check --all-targets with CUDA 13.2 and all optional CUDA libraries passed for std and no-std. Existing warnings remain. Formatting and diff whitespace checks passed. GPU graph replay and active consumer builds remain required before deployment. Tracking issue: https://github.com/geodesa-ai/hedron/issues/1362.
