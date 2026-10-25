# Provisional isolation backend choice for a public execution pool

Status: Draft (2026-10-25).

Decision date: 2026-10-24. No activation or qualification.
Primary-source reconciliation supports this first qualification target, with the
corrections below. The choice remains provisional pending owner decisions and
exact-tuple qualification. Contradictory findings reopen the choice and block
activation; documentary acceptance cannot waive that gate.

Recommend **Firecracker on dedicated bare-metal Linux/KVM** as the first tuple
to qualify: a fresh cold-boot microVM per job and one live job per physical host.
Recommend **no automatic fallback**. Defer both Apple-hardware offerings pending
separate qualification; Linux guests and macOS guests are distinct tuples.
These are the policies traced as R-01–R-13 below, not claims of deployed coverage.

## Inputs and interpretation

The [accepted threat model](threat-model.md) supplies 28 threats and 25 controls.
The [backend evidence register](isolation-backends.md) supplies the bracketed
source IDs used here, 15 configurations and U01–U25. Those IDs retain their
pinned revisions and read dates; this document does not replace that register.

This decision is reconciled with [CIP-0025](https://github.com/relux-works/curator-spec/blob/dea17185c0317892c13dda823baaeded684f6352/cips/CIP-0025-execution-isolation-contract.md)
and [CIP-0026](https://github.com/relux-works/curator-spec/blob/88b0d8615a5df8728e0e9540a9edd708c0ca5660/cips/CIP-0026-foreign-code-execution.md).
The 23 comparison groups below map to their current shared and pool-local IDs;
PR-01–PR-23 retain their property meaning. CIP-0026 sets the pool's security-
revocation bound at no more than 300 seconds from detection, including required
confirmation and quiet periods. A longer bound requires the explicit, bounded,
current independently authenticated owner exception specified there, bound to the
exact scope and exposed in admission evidence. Retry cannot extend the clock.
Publication of these CIPs selects no backend and qualifies or activates nothing.
Changed normative meanings or rollout rules require reconciliation before admission.

Mechanism facts and limitations are sourced; ranking and restrictions are this
document's policy judgments. The table below assesses **backend contribution**,
not an admitted capability vector. `provided` is reserved for a qualified whole
property, and no entry here claims it. `weaker, with compensation` identifies a
partial mechanism, missing part and proposed pool control, not a qualified weaker
level. `not provided` means this backend/scope does not supply the whole property;
`unknown` means the reviewed evidence does not establish it.

For actual CIP admission, unknown and compensation-dependent entries remain
**PENDING-VERIFY** until the complete tuple is qualified. A not-provided required
property refuses. Operator acceptance cannot turn unverified evidence into
qualification. Any later qualified weaker entry requires the CIP's exact operator
acceptance and requester acknowledgement; EIC §11 never makes products equal.

The three surviving evaluation configurations are Firecracker/Linux (first
qualification target), direct VZ/Linux (deferred) and direct VZ/macOS (deferred).
Survival means worth testing under the stated scope, not permission to run jobs.

## Decision criteria and traceable policies

A backend must contain hostile guest code and protect its host interfaces even
when guest root is hostile. The pool must still contain a compromised or dishonest
host externally. AD-04 and AD-06 therefore preclude promises of confidential
execution or truthful results from an ordinary VM. [F01] [K07] [A05]

The following rows define every recommended policy, its threat basis and evidence.
Criteria are cumulative: a fast boot cannot offset missing containment or cleanup.

| Policy | Recommended property and decision criterion | Threat trace | Evidence |
| --- | --- | --- | --- |
| R-01 | Require a separate Linux guest-kernel VM boundary for the initial Linux class; qualify minimal Firecracker/KVM first. For containment against hostile guest root, the guest kernel is untrusted: the boundary relies on the hardware/firmware, host kernel/KVM, the VMM, jailer/seccomp constraints and protected host integration. Pin the guest kernel and image for compatibility and provenance; their honesty must not be a prerequisite for containing the guest. Any guarantee that relies on enforcement inside the guest must separately state its guest-kernel and agent trust assumptions. | TM-ISO-01, TM-SUP-01 | [F01] [F02] [F03] [F09] |
| R-02 | One cold-boot VM per job; never pool live VMs or writable guest state across jobs. Complete stop and tested disposal precede any reuse. | TM-DATA-01, TM-LIFE-01, TM-SUP-02 | [F03] [F05] |
| R-03 | One live job per dedicated physical host initially, including every enclosing layer; no unverifiable parent/co-tenant topology. No advanced side-channel elimination claim. | TM-SIDE-01, TM-DATA-03 | [F02] [N14] |
| R-04 | CPU-only guests, private block inputs/output, no host-directory shares, GPU/passthrough or guest-visible runtime/control sockets. Keep only admitted virtual devices/channels. | TM-ISO-02, TM-DATA-03, TM-CRED-03 | [F01] [F03] [F07] |
| R-05 | Permit only the stated root setup/stop/cleanup authority and unprivileged per-job VMM UID; reject a requirement for an entirely root-free host stack. | TM-ISO-01, TM-ISO-02, TM-DOS-01 | [F03] [F10] [K04] [G07] |
| R-06 | Independent upstream default-deny egress and scoped transfer gateway, including host-origin traffic after escape; no platform or injected job secrets. | TM-NET-01, TM-NET-02, TM-NET-03, TM-CRED-01, TM-CRED-02 | [F02] [G04] [A06] |
| R-07 | Hard whole-job CPU/memory/storage/output limits, external elapsed/work budgets and measured stop/recovery reserve before admission. Guest sizing and shares alone cannot qualify. | TM-DOS-01, TM-DOS-02, TM-TIME-01 | [F02] [N09] [G05] |
| R-08 | Defer both Mac classes; qualify Linux guest and macOS guest separately, including full host limits, privileges and applicable license/service permissions. | TM-QUAL-01, TM-OPS-01, TM-DOS-01 | [A02] [A04] [A05] [A10] |
| R-09 | No runtime fallback or borrowed evidence; unavailable KVM, changed tuple or incompatible workload refuses or queues for a separately admitted tuple. | TM-AUTH-01, TM-QUAL-02, TM-LIFE-01 | [F09] [G02] [K04] [Q01] |
| R-10 | Offer only bound host-reported results initially; stronger correctness needs separately trusted verification. Disclose operator access and retention. | TM-RES-01, TM-RES-03, TM-OPS-01 | [K07] [G11] [A05] |
| R-11 | Independent fact confirmation, exact-tuple review and all required negative/positive observations precede activation; revoke on material drift. | TM-QUAL-01, TM-QUAL-02, TM-SUP-01 | [F09] [FS01] [G11] |
| R-12 | Opaque guest job formats stay inside the boundary; every unavoidable external interpreter has constrained private staging, streaming limits and inert output consumption. | TM-DATA-05, TM-DATA-04, TM-RES-02 | [F03] [KS11] [LS02] |
| R-13 | Disable memory snapshot/restore, warm cloning and stateful reuse initially; immutable base images still require provenance and cannot carry job state. | TM-DATA-01, TM-DATA-02, TM-LIFE-01 | [F05] [F06] |

Backend obligations carry the execution barrier, device exposure and observable
start/stop state. The host adapter carries image/device configuration, all charged
workers, stop, disposal and local policy. The independent pool services carry
admission, authentication, provenance, external time, upstream network containment,
transfer/result handling and qualification/revocation. R-01–R-13 do not move those
external responsibilities into a VMM. [F01] [F02] [F03] [K07]

The hard operational minimum is usable KVM on a supported pinned Linux/CPU/kernel
combination, an ephemeral per-job unit, auditable control interfaces and bounded
privileged setup/cleanup. A dedicated 8-vCPU/32-GiB host is a sizing example, not a
qualification tuple or assurance level. Zero privileged setup is incompatible with
the selected jailer path; that requirement would reopen D-01/D-05. [F03] [F09]

## Guarantee comparison against the pool CIP

Each of the 23 groups maps to current CIP IDs and retains its threat mapping. Every candidate cell
cites existing evidence, including cells that deliberately declare non-provision.
The final column names the missing pool contribution and its direct property
observable; it does not claim that contribution is implemented or qualified.

| Guarantee ID | Firecracker/Linux | VZ/Linux | VZ/macOS | Threats | Required pool contribution and observable |
| --- | --- | --- | --- | --- | --- |
| `isolation.read` | weaker, with compensation: KVM/guest boundary; exported disks and control paths still need qualification. [F01] [F03] | weaker, with compensation: guest kernel boundary; device/share configuration remains trusted. [A01] [A07] | weaker, with compensation: guest boundary; auxiliary storage and host shares need control. [A02] [A07] | TM-ISO-01, TM-ISO-02, TM-DATA-04 | Launcher and transfer gateway enforce exact input scope; PR-01 observes denied bytes, independently of write denial. |
| `pool.isolation.write` | weaker, with compensation: jailed VMM/private block devices; host image preparation is separate. [F01] [F03] | weaker, with compensation: private guest disk; optional host shares widen authority. [A05] [A07] | weaker, with compensation: private guest disk/auxiliary storage; host exports require restriction. [A02] [A07] | TM-ISO-01, TM-DATA-05 | Launcher and constrained preparer; PR-02 checks protected bytes and metadata, not merely error codes. |
| `ipc.isolation`, `execution.descriptors` | weaker, with compensation: jail/namespaces and minimal devices; no inherited-control guarantee without inventory. [F03] | unknown: entitlement/API inventory does not establish every helper/handle boundary. [A05] [A06] | unknown: same caller/helper uncertainty plus guest management path. [A02] [A05] | TM-ISO-02, TM-CRED-03 | Launcher inventories descriptors/namespaces; no management API or host signer socket reaches the job; PR-03. |
| `pool.isolation.residue` | unknown: VMM destruction leaves disks, logs, swap and snapshot debt. [F02] [F03] [F05] | unknown: disks/saved state/shares survive object deletion. [A05] [A08] | unknown: auxiliary storage, identities, disks and saved memory require disposal. [A02] [A08] | TM-DATA-01, TM-DATA-03, TM-SUP-02 | Lifecycle owner must complete qualified removal/reset by its bound and prove PR-04; quarantine alone is not cleanup completion. |
| `pool.isolation.exclusive` | not provided: one VMM does not reserve the physical host. [F02] | not provided: one VM does not reserve all hardware/co-tenancy. [A05] | not provided: guest count is not physical allocation proof. [A05] [A10] | TM-SIDE-01 | External scheduler/allocation authority; initial one-job physical-host rule and PR-05, including admission races. |
| `pool.isolation.side-channel` | not provided: advanced hardware leakage elimination excluded. [F02] | not provided: hardware leakage elimination is outside this offer. [A05] | not provided: same explicit exclusion; macOS guest changes no promise. [A05] | TM-SIDE-01 | PR-06 requires honest unavailable status; exclusivity reduces exposure without converting it to elimination. |
| `network.egress` | weaker, with compensation: TAP/filtering point and token buckets; no complete destination policy. [F01] [F02] | weaker, with compensation: NAT path; NAT is not an allowlist. [A06] | weaker, with compensation: NAT path still needs independent policy. [A06] | TM-NET-01, TM-NET-02 | External egress gateway covers host-origin traffic after escape, DNS/redirects/address families and traffic bounds; PR-07. |
| `pool.network.ingress` | weaker, with compensation: host-facing virtual NIC; transfer authentication is separate. [F01] [F02] | weaker, with compensation: NAT does not establish transfer authority. [A06] | weaker, with compensation: same NAT/authentication distinction. [A06] | TM-NET-03 | Network/transfer gateway denies unsolicited access and authenticates scoped transfers; PR-08. |
| `pool.credential.absent` | not provided: guest isolation cannot remove credentials provisioned on host or image. [F01] [F03] | not provided: framework configuration does not enforce fleet credential absence. [A05] | not provided: macOS guest capability does not enforce credential absence. [A02] [A05] | TM-CRED-01, TM-CRED-02 | Image/host inventory and broker refusal; no injected job secrets, provider keys or platform identity; PR-09. |
| `pool.identity.separated` | weaker, with compensation: guest kernel and VMM UID differ from host authority; protocol still needed. [F01] [F03] | weaker, with compensation: guest execution is separate; peer-authority bridging is not supplied. [A01] [A05] | weaker, with compensation: guest identity is not a host peer UID. [A02] [A05] | TM-CRED-03, TM-AUTH-02 | Authority gateway and launcher; no signer/registrar forwarding or guest-to-host UID translation; PR-10. |
| `process.stop` | weaker, with compensation: dedicated VMM can terminate; workers/helpers and quiet-period absence need proof. [F02] [F03] | weaker, with compensation: VM lifecycle exists; helper/slot completion unqualified. [A01] [A10] | weaker, with compensation: VM lifecycle exists; complete stop/slot recovery unqualified. [A02] [A10] | TM-DOS-01, TM-LIFE-01 | Supervisor must actually reach whole-runner absence by original deadline; PR-11/FX-22, not leader exit or fencing only. |
| `limit.cpu`, `pool.limit.elapsed-work` | weaker, with compensation: host CPU bandwidth controls; indirect workers and elapsed-work ceilings remain external. [F02] [N09] | unknown: vCPU count does not establish whole-job CPU/work ceilings. [A05] | unknown: vCPU sizing is not whole-job host accounting. [A05] | TM-DOS-01 | Resource supervisor combines quota, cumulative work, external deadline and completed stop; PR-12. |
| `limit.memory` | weaker, with compensation: guest RAM plus outer cgroup; host charges/recovery require measurement. [F02] [N09] | unknown: guest RAM does not cap all helpers or prove recovery reserve. [A05] | unknown: same whole-job memory gap. [A05] | TM-DOS-01 | Resource supervisor accounts VMM/workers/caches, explicit swap policy and actual saturated recovery; PR-13. |
| `limit.disk`, `limit.io`, `pool.limit.storage` | weaker, with compensation: block capacity/IO throttling; logs, inodes and retained debt need outer limits. [F01] [F02] | unknown: disk size does not bound host files, snapshots and retained debt. [A05] [A08] | unknown: disk/auxiliary state accounting incomplete. [A02] [A08] | TM-DOS-01, TM-DOS-02 | Storage/lifecycle owner caps bytes, inodes, IOPS and all retained debt across attempts; PR-14. |
| `pool.limit.output` | not provided: serial/log paths can consume host resources; intake parsing is outside VMM. [F02] | not provided: framework does not supply complete output/parser budgets. [A05] | not provided: same external collector requirement. [A05] | TM-RES-02, TM-DOS-02 | Collector/telemetry service bounds streaming bytes/count/expansion/work, declares truncation, publishes atomically; PR-15. |
| `pool.time.authority` | not provided: VM state/clock is not trusted external grant validity. [F05] | not provided: VM clock/state is not authority freshness. [A08] | not provided: saved state cannot establish current authority. [A08] | TM-TIME-01 | External authority time and rollback-safe journal preserve original expiry/work budgets; PR-16. |
| `pool.input.integrity` | weaker, with compensation: private block inputs permit a read-only design; authorization/provenance stay external. [F01] [F03] | weaker, with compensation: configured storage/shares permit scoped inputs; digest authorization external. [A05] [A07] | weaker, with compensation: image/restore inputs need approved exact provenance. [A02] | TM-DATA-02, TM-DATA-04, TM-SUP-01 | Image/transfer owner verifies exact approved bytes, complete dependency manifest and object authorization; PR-17. |
| `pool.input.interpretation` | not provided: jail/VMM does not constrain every image/preparation/collector interpreter. [F03] [KS11] | not provided: device APIs do not constrain every host-side parser. [A05] [LS02] | not provided: restore/image/collector interpreters remain separate. [A02] [LS02] | TM-DATA-05 | TC-25 inventory and constrained private staging for every applicable interpreter, including guest I/O and telemetry; PR-18. |
| `pool.result.provenance` | not provided: host-controlled VMM/output is no attempt-binding or truth protocol. [F01] [K07] | not provided: host controls ordinary VM and output. [A05] [K07] | not provided: macOS guest supplies no independent result attestation. [A05] [K07] | TM-RES-01, TM-RES-03 | Trusted collector binds all identities/dependencies/digests and discloses host-reported level; PR-19. |
| `pool.result.inert` | not provided: backend does not constrain all downstream import/review behavior. [F01] [G11] | not provided: VM API does not make returned bytes inert downstream. [A05] | not provided: same collector/import/review boundary. [A05] | TM-RES-02 | Inert collector/importer/renderer, no hooks or discovery, including telemetry; PR-20. |
| `pool.journal.durable` | not provided: VM snapshots are not fenced durable effect/transfer journals. [F05] | not provided: VM saved state is not a pool effect journal. [A08] | not provided: same restore/replay gap. [A08] | TM-LIFE-01 | External durable lifecycle/transfer journal preserves original effect identity and blob obligations; PR-21. |
| `pool.control.bounded` | not provided: VMM limits do not protect external admission/transfer/recovery capacity. [F02] | not provided: guest sizing does not bound external control work. [A05] | not provided: same control-service gap. [A05] | TM-DOS-02 | Admission/transfer/control owners cap per-principal and global debt; demonstrate independent recovery completion under saturation; PR-22. |
| `update.requalified`, `qualification.revocation` | not provided: supported kernel/release is not a current qualification/revocation registry. [F09] [FS01] | not provided: OS/framework delivery is not tuple requalification. [AS01] [A05] | not provided: guest/host OS changes do not preserve qualification automatically. [AS01] [A02] | TM-QUAL-01, TM-QUAL-02 | Qualification authority invalidates changed tuple, fences starts and completes active-work disposition by revocation bound; PR-23. |

## Linux primary configuration and alternatives

**Primary qualification target: Firecracker/Linux**, subject to R-01–R-07 and R-09–R-13.
Use a dedicated bare-metal host with a current approved host kernel/microcode and
usable KVM. Start with the supported architecture and 4-KiB-page kernel matrix in
F09; pin the exact build and all security fixes during qualification. The evidence
sample's release label alone is not a safe bill of materials. [F09] [FR01] [FS01]

The proposed tuple includes CPU model/topology/firmware, Linux build/configuration,
KVM, Firecracker and jailer binary/seccomp digests, guest kernel/root image,
launcher/collector versions, UID scheme, all resource policies, storage layout,
network gateway/filter configuration, interpreter inventory and qualification
harness. Record CPU mitigation policy, firmware/microcode, SMT and memory-
deduplication state, including drift detection. Unknown or changed mandatory
mitigation state closes admission. None of the unresolved exact values may
become a wildcard at admission.
This is the EIC §10/§11 binding discipline for R-09/R-11, not a new tuple protocol.

For each job, cold boot one VM with a unique VMM UID/GID and private jail,
read-only approved root/input block images and a bounded private output disk.
Use only required virtual devices; omit optional PCI/vhost-user/metadata/GPU and
host-directory export features from this initial profile. A guest API/status
channel is narrowly declared, bounded and treated as hostile bytes; it never
exposes the host management API. These are R-02/R-04/R-12 restrictions to qualify,
not a claim that every Firecracker default already enforces them. [F01] [F03] [F07]

Cold boot does not sanitize a replicated root image. Image acceptance must cover embedded machine identities, random seeds, credentials and prior workload state, as well as publisher/build provenance and revocation. Qualify early-boot entropy readiness and per-execution identity for the actual guest stack. A small cohort with no duplicates is a regression observation, not proof of randomness. [F05] [F06]

Admit only the approved production build, exact binary and restrictive filter identities, with an explicit inventory of all execution and guest-facing threads. No guest instruction may run before all filters required by the admitted profile are installed. Qualification may use reviewed source-to-binary provenance to establish installation ordering; a post-start thread-status observation is corroborating evidence, not a substitute for that ordering or for filter identity. Firecracker v1.17.0 installs each vCPU's own filter before its first KVM_RUN, but its stock cold-boot path resumes vCPUs before installing the main VMM filter and provides no external all-thread verify-then-release step. Do not mark that path as satisfying this gate without separately qualified evidence of an equivalent pre-execution boundary. Keep admission closed when the required ordering is unavailable. Use operator-controlled jailer inputs and protected setup paths, a fresh job identity and an explicit per-job VMM UID/GID. Record the privileged supervisor's setup, observation, stop and cleanup authority separately from the VMM's runtime identity.

This ordering assessment uses Firecracker v1.17.0 source at
`95f868c8e345b1cc8faccd1a3c910b4989dc3f58`, not a release-label approval.
The [vCPU filter is installed before its run loop](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/vstate/vcpu.rs#L217);
an installation error panics and the [production panic policy aborts the process](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/Cargo.toml#L37).
However, [cold boot resumes vCPUs](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/builder.rs#L373)
before [the API caller installs the main VMM filter](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/firecracker/src/api_server_adapter.rs#L222).
Thus a guest may run while the main VMM thread remains unfiltered. The
[JSON/no-API path has the same ordering](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/firecracker/src/main.rs#L642).
Stock cold boot fails the strict all-required-filters-before-any-guest-instruction
gate and remains **PENDING-VERIFY**, with admission closed. This is not a claim
that a vCPU runs without its required approved filter or that seccomp cannot be
verified. Source ordering establishes a property only for the exact reviewed
production build, restrictive filters and declared trusted-host/kernel assumptions;
post-start observations do not repair a violated order.

**K4 owner options (unresolved; D-01/D-05).** Each option requires an explicit
record, surface analysis and independent qualification; none is approved here:

1. Qualify a launcher/patch that installs the main VMM filter before vCPU resume,
   then verifies the complete required thread/filter state before guest release.
   Merely wrapping the stock start request does not change its ordering.
2. Qualify [paused snapshot restore with `resume_vm=false`](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/rpc_interface.rs#L643)
   as a separate profile with its own verification/release step, snapshot trust,
   residual-state, identity and entropy acceptance; it is outside the cold-only class.
3. Explicitly revise the contract to accept a post-start check as a weaker temporal
   guarantee. It cannot satisfy or be relabeled as the current strict gate.
4. Revisit the boundary class and compare alternatives under the same requirements.
5. Explicitly choose the narrower contract “every thread filtered before it handles
   guest-controlled input,” with an analysis of every main-thread surface before
   its event loop and every other execution/guest-facing thread. This is an owner
   choice of a weaker timing contract, not a silent replacement for the strict gate.

The jailer starts as host root and drops the VMM UID/GID; the complete minimal
capability set is not established by this evidence. [F02] [F03]

Whole-job host cgroup v2 policy must be configured explicitly; the jailer's
reviewed default is v1. Bound all VMM/helpers/attributable workers and logs/storage,
not only guest RAM and vCPU count. IO/network token buckets are rate controls,
not destination or capacity quotas. Supervisor resource reservation must be proved
by actual recovery completion under saturation. This is R-07. [F02] [F03] [N09]

A successful jailer exit or VMM process start does not prove cgroup application. In the documented cgroup-v2 path with no `--cgroup` arguments, the process can continue without moving when the requested parent cgroup is absent. The pool launcher must verify the actual applied host cgroup, required controllers, effective limits and complete charged-process scope before `InstanceStart` or any job-controlled instruction. Missing or mismatched applied state refuses admission. [F03]

Record the host scheduling classes and the actual placement and charging of every attributable worker. The cgroup v2 CPU bandwidth limit is not a universal limit on arbitrary scheduling classes, and `memory.max` permits documented transient overshoot; publish the measured enforcement envelope and the usable recovery reserve. Physical job exclusivity does not remove the host kernel or trusted control work from the machine: record CPU mitigation policy, firmware/microcode, SMT and memory-deduplication state as part of the admitted tuple. Changed or unknown mandatory mitigation state closes admission; no finite test establishes elimination of all hardware side channels. An enforcement envelope must fit the requested hard ceiling: transient
overshoot is not permission to exceed an admitted cap. Reserve headroom or refuse
a ceiling the measured tuple cannot enforce. [F02] [N09]

Host PID limits count host VMM/helper tasks, not the guest process population.
Guest-root-managed PID policy is not an outer hard guarantee. Under R-07, bound
host tasks and guest effects through qualified outer CPU/memory controls; a request
for an unqualified strict guest process-count ceiling must refuse. [N09] [F01]

**Per-job and concurrency rule:** one VM per job, one live job per physical host,
no cross-job live-VM reuse and no automatic second job while stop/disposal/export
is pending. Trusted control work still needs its own reserve. Physical exclusivity
must include a virtual host's parent; a separately hosted VM without verifiable
exclusive parent allocation cannot satisfy this initial class. [F02] [N14]

Stopping the VMM is insufficient to release the host. By the published original
bound, stop all owned execution, observe the quiet period, reconcile exports and
actually dispose/reset the declared state. An unknown outcome fences reuse;
missing timely completion fails qualification even if the host stays quarantined.
A later rebuild/re-enrollment is recovery, not evidence that the missed bound was
met or that storage/firmware was securely erased. [F02] [F03] [F05]

Distinguish lifecycle completion under a trusted host kernel from containment after host compromise. A compromised host's own stop, health or cleanup report is not independent completion evidence. External admission and network fences must remain effective. If bounded physical stop is promised for this adversary, specify and qualify an independently controlled mechanism and observer; otherwise retain unknown completion and prohibit reuse. Re-enrollment requires separately authorized recovery evidence and does not retroactively satisfy a missed deadline. [F02] [K07]

**Fallback: none initially** (R-09). If hardware, syscall/tool compatibility,
resource or cleanup qualification fails, refuse that placement or keep the job
queued. A distinct admitted implementation needs a new comparison and evidence;
there is no silent systrap, TCG, native-host or shared-VM substitution.

The initial workload inventory is not yet fixed. Statements below that an integration or device is not required are pilot-scope assumptions for owner confirmation, not facts established by upstream documentation. Record representative build and test workloads, required ABIs and devices, and CRI, debugger, nested-container and Mac requirements before using those assumptions to eliminate a candidate.

| Alternative | Why it loses for the initial scope; reconsideration trigger |
| --- | --- |
| gVisor/systrap | Supported and useful without KVM, but it adds a Sentry ABI barrier rather than the selected ordinary Linux guest-kernel boundary. A different approved class could qualify it later; compatibility and whole-host controls remain obligations. Built-in rootless convenience mode is not a pool-limit solution. [G01] [G02] [G07] |
| gVisor/KVM | Also hardware virtualization: KVM supports Sentry execution, with a different ABI/device boundary from an ordinary Linux guest-kernel VM. Hardware backing is not its exclusion criterion. Direct Firecracker is the first target for the selected guest-kernel class; reconsider Sentry with concrete workloads or matched compatibility, integration or performance evidence. This is scope, not a security ranking. [G02] [G09] |
| gVisor/ptrace | Unsupported historical platform; not a current security fallback. [G02] |
| Kata/QEMU | Reconsider if CRI integration, QEMU-specific devices or its virtio-fs path are required. Shim/agent and any enabled file-sharing services add integration work beyond the initial private-block tuple. The documented runtime-rs rootless VMM path still has root shim/virtiofsd services. This is a scope/TCB preference, not an insecurity claim. [K01] [K04] [K05] |
| Kata/Cloud Hypervisor | Reconsider if CRI integration or this VMM's virtio-fs path is required. Qualify its shim/agent/VMM and any enabled file services separately; no initial workload requires that extra integration. Its documented non-confidential runtime-rs rootless VMM path still leaves shim/virtiofsd at host root. [K01] [K04] [K05] |
| Kata/Firecracker | No initial workload requires Kata's pod/agent orchestration or block/snapshotter integration in addition to direct Firecracker. Reconsider when that integration is needed and separately qualified; direct-Firecracker evidence cannot qualify its added layers. [K01] [K04] [K10] |
| Kata/Dragonball | This integrated shim/VMM is a distinct boundary with its own qualification and unresolved complete privilege contract; it is outside the documented rootless-VMM support list. No initial pod/agent requirement justifies another tuple. Reconsider with a documented privilege/lifecycle contract and qualification; do not inherit Firecracker evidence. [K01] [K04] |
| Native Linux or macOS sandbox profiles | Reference controls only; shared host kernel is outside the initial separate guest-kernel VM policy. They remain useful inside the trusted host design, not a substitute pool boundary. [N11] [N12] [N05] |
| Lima/VZ | Host/guest agents, configured sharing and network paths add wrapper integration beyond direct VZ. Reconsider if Lima instance management is needed and its selected sharing/network/lifecycle profile is qualified. A proposed direct-VZ adapter is not already proven safer. [L14] [L06] |
| Lima/QEMU with HVF | The Lima agent/template/network surfaces plus the selected QEMU machine/device set need a separate Mac tuple; no initial workload requires this combination. Reconsider for a demonstrated QEMU-specific requirement and qualified host limits/cleanup; do not borrow direct-VZ evidence. [L14] [L06] [Q01] |
| Tart | Its image/VM workflow and applicable software-license/service questions need their own review and qualification. No initial Mac workload justifies this additional wrapper tuple over evaluating direct VZ; this is a policy preference, not proof that a new direct adapter is safer. Reconsider when that workflow is required and the gates close. [T02] [T03] |
| Apple container/Linux | Per-container VMs are relevant later, but helpers/network/store and host-memory reclamation still need qualification; it cannot serve macOS/Xcode jobs. No independent whole-host quota advantage is established. [C01] [C02] [C03] |
| Lima/krunkit | Lima documents an experimental, GPU-focused macOS driver; no initial CPU-only workload requires that integration. Reconsider for a needed driver/workload after separately qualifying its selected devices, host limits and lifecycle. This does not characterize standalone krunvm. [L10] [V02] |
| Standalone krunvm | Separate Linux microVM CLI using OCI images, libkrun and buildah, with Linux/KVM and macOS/Hypervisor.framework host paths. Host-volume mapping and guest-port exposure need an admitted profile; this evidence cut leaves end-to-end pool egress, cleanup, resource and advisory coverage unknown. Reconsider with a documented and independently qualified pool integration; GPU requirements or Lima's experimental label are not its exclusion reason. [V01] |

## Apple hardware: two deferred offerings

Under R-08, **VZ/Linux** is a candidate for arm64 Linux workloads on a dedicated
Apple-silicon host. **VZ/macOS** is a separate candidate only where Xcode or Darwin
APIs are required. Each uses a fresh VM and the R-02/R-03 exclusive-host policy;
neither is an automatic Linux fallback or a presently supported pool class.
A minimal direct framework application is a proposed adapter, not an existing
qualified service. Its development and qualification cost must be budgeted. [A01] [A02]

Linux guests cannot provide Xcode/macOS-framework or macOS/iOS Simulator execution.
Rosetta for Linux binaries is not an x86 macOS guest. Pin host/guest architecture,
restore image, auxiliary storage and Xcode/OS pairing independently. [A02] [A09] [L02]

The macOS Tahoe 26 SLA §2B(iii) conditionally allows two additional virtual macOS
copies on qualifying Apple hardware already running the software; this does not
clear public multitenant service use. §3's leasing route includes minimum 24-hour
terms, end-user exclusivity, acceptance and advance Apple notice. §3D distinguishes
lessor and lessee virtualization and limits a lessor's provisioning copy to one.
The actual deployed license/agreement and service model require an owner decision;
this paragraph grants no service permission. [A04]

License allowance, technical VM slots and admission capacity are independent.
The API exposes a VM-limit error without a universal numeric limit in the reviewed
symbol. Our proposed allocation is at most one job VM per physical Mac, regardless
of any larger license or technical allowance; slot recovery must still be tested.
Guest RAM/vCPU settings do not establish whole-host hard ceilings. [A10] [A05]

A05/A06 establish entitlement/configuration facts, not caller/helper UIDs or absence
of privileged OS services. A real adapter must document launch/stop/cleanup authority
and outer quotas before either Mac candidate can proceed. Linux-only Mac demand
alone does not justify activating a class with unproven hard limits. [A05] [A06]

None of these ordinary configurations can offer confidentiality from the host or
platform operator, hardware-side-channel elimination, host-independent truth of a
build result, or immunity from unknown escapes. Those are unavailable service
promises under R-03/R-10; choosing a macOS guest does not change them. [K07] [A05]

## Cost and host topology

No measurements were made here. Keep image fetch, cold boot, agent readiness,
workload completion, export and completed cleanup as separate latency distributions.
The latency relevant to another job includes completed cleanup, not just VMM exit.

| Host or cost item | Published evidence or explicit estimate | Decision consequence |
| --- | --- | --- |
| Firecracker v1.17.0 specification | Conditional minimal-VM targets: at most 125 ms from `InstanceStart` to guest `/sbin/init`; at most 5 MiB VMM-thread overhead for the stated 1-vCPU/128-MiB configuration and reference hosts with resources available. [F13] | These are not pool image, build readiness, helpers, storage, export or completed-cleanup measurements and do not justify the 0.5-GiB whole-job allowance. |
| Firecracker historical startup | The maintainer's 2020 paper reports under 125 ms to application code and under 5 MB VMM overhead for a minimal setup, not a guest-RAM budget; production context differs and the paper's host is much larger. [B01] | Historical evidence, not a current build-job percentile or a whole-host overhead reservation. |
| Initial Linux sizing | Estimate: 8 vCPU/32 GiB, one 2-vCPU/2-GiB guest, assumed 0.5 GiB VMM/helper overhead and 4 GiB protected host reserve; about 6.5 GiB budgeted before images/caches/IO peaks. [F02] [N09] | Every number except host class is a test-planning assumption. Hard charged-memory cap and reserve must be measured; no production sizing claim. |
| Initial throughput | Estimate: one job per host; approximate serial capacity is 1 / (boot + job + export + completed cleanup + admission delay). [F03] [F05] | The 8-vCPU label does not authorize four or eight concurrent tenants. SMT/physical topology and mitigation policy can reduce usable CPU. [F02] |
| Image/storage/network | Private input/output disks, bounded logs, immutable image cache and independent gateways add storage/IO/transfer cost; no matched price/IOPS figure established. [F01] [F02] | Reserve storage for recovery and retained debt. A small guest root disk cannot bound logs, exports or parent-host debt. |
| Apple-silicon Mac | Unknown matched boot/RSS and whole-job cap. Start planning with one guest per physical Mac; CPU/RAM/disk knobs are not complete host accounting. [A05] [A10] | No density or cost-parity claim; no hardware purchase implied. Add adapter/qualification and applicable service licensing cost. [A04] |
| Apple container/Containerization | Apple Containerization's sub-second claim lacks a pool workload distribution; freed guest memory may remain resident until restart. [C02] [C03] | It does not establish a faster or denser qualified service than direct VZ or Linux/KVM. |

A pool host that is itself a VM is a **composed** tuple. KVM must be exposed by the
outer hypervisor with compatible CPU/kernel features; nesting adds a trusted layer,
accounting uncertainty and potentially overhead. No measured nested multiplier is
available. No KVM means no Firecracker launch, not permission to switch to TCG.
Parent topology and foreign co-tenants must be independently known. [N14] [F09] [Q01]

Apple's generic nested API needs the documented M3+/macOS-15 capability path; this
is not proof of supported nested macOS service or nested Firecracker/Kata operation.
Defer composed deployments initially. Any later offer separately qualifies every
layer and preserves the exact exclusive/shared requirements. [A03] [T03] [N14]

## Unproven activation gates, in priority order

R-11 requires independent fact confirmation of the corrected decision and exact
production evidence. A disposable representative pool test machine must exercise
the production tuple; documentary reconciliation is not runtime qualification. The following 22 U-items are
necessary for the initial Firecracker recommendation. All remain **unrun**.
The negatives are bounded policy/fixture probes, not vulnerability reproduction.

Each row needs its own direct host/external observable and nearby valid control;
setup failure, skipped/truncated logs or a hung operation is not a killed control.
CIP PR-01–PR-23 and FX cases remain additional obligations: these U-items are not
complete coverage of every external interpreter, credential path or authority gate.
For stop, cleanup, recovery and revocation, fencing plus unknown is safety only;
the prescribed operation must actually complete by the original bound to qualify.

| Priority | U-item | Threats | Negative and required observable | Nearby positive control | Bound and evidence |
| --- | --- | --- | --- | --- | --- |
| P0 | U04 | TM-AUTH-01, TM-DATA-05 | Disallowed mounts/runtime flags/templates, snapshot or device requests refuse before any VM/helper side effect. | Equivalent job with protected admitted configuration launches. | Admission and exact entrypoint; FX-06/21. [F03] [G11] |
| P0 | U01 | TM-ISO-02, TM-CRED-03 | Job cannot open host KVM/runtime/control sockets or undeclared inherited handles; no host response/effect. | Guest null/entropy and the declared bounded output channel work. | Separate read/IPC observations; PR-03. [F03] [G09] |
| P0 | U02 | TM-ISO-01, TM-DATA-04 | Reads of unrelated synthetic host/job markers return no marker bytes in outputs/logs; no write is needed to test this. | Exact own-input bytes return through the same admitted path. | PR-01 confidentiality oracle, independent of writes. [F01] [F03] |
| P0 | U03 | TM-ISO-01, TM-DATA-02 | Forbidden writes/renames leave protected host/base-image bytes and metadata unchanged after restart. | Private output writes/readback succeed. | PR-02/17; immutable image control. [F01] [F03] |
| P0 | U05 | TM-DATA-05, TM-SUP-01 | Benign external-reference image fixture rejects before unrelated synthetic file access, host effects or partial publication. | Same supported format without the external reference imports wholly. | Sample only; full TC-25 interpreter inventory required. [HS02] [LS02] |
| P0 | U24 | TM-DATA-03, TM-ISO-02 | GPU/raw-block/passthrough or unadmitted channel requests refuse; no such device appears in the CPU-only guest. | Allowed virtual block/entropy IO works. | No device-reset qualification inferred from omission. [F01] [K02] |
| P0 | U06 | TM-NET-01, TM-NET-02 | Independent outer capture sees no denied address/DNS/proxy-route traffic, including IPv6 and gateway failure. | Same transport reaches only an explicitly allowed fixture. | PR-07; after-escape host-origin containment also required. [F02] [G04] |
| P0 | U07 | TM-ISO-02, TM-NET-02 | Synthetic host/LAN/metadata/peer endpoints get no request or effect through any admitted route. | Allowed dependency/transfer endpoint responds. | Host loopback flag alone is insufficient. [F02] [L09] |
| P0 | U13 | TM-LIFE-01, TM-DOS-01 | Cancel a bounded child/IO workload; host-owned execution and network activity reach zero through quiet period by original bound. | Ordinary exit and a transient-unknown cancellation complete within their bounds. | PR-11/FX-22; not just VMM leader exit. [F02] [F03] |
| P0 | U14 | TM-LIFE-01, TM-DATA-01 | Interrupt each disposal/export checkpoint: no duplicate start/reuse; actual timely recovery disposes state, while missed completion fails qualification. | Uninterrupted and delayed-within-bound lifecycle complete exports and disposal before release. | Quarantine is safety, not completion; PR-04/21. [F03] [F05] |
| P0 | U15 | TM-DATA-01, TM-SUP-02 | Prior synthetic disk/memory/cache/swap/log markers are absent from successor-visible bytes; independent inventory confirms actual removal/reset. | After timely disposal, successor reads its own marker; predecessor retained output is only in its authorized external store. | PR-04; no forensic-erasure proof or mere retrieval-refusal substitution. [F02] [F05] |
| P0 | U20 | TM-ISO-01, TM-DOS-01 | Absent/wrong actual cgroup, controllers, effective limits or charged membership refuses before `InstanceStart`/any job instruction, including cgroup-v2/no-arguments/absent-parent setup despite successful process start. Unapproved build or missing/mismatched applied thread filters also refuses. | Approved production binary/filter identities, actual charged scope, applied filters and per-job VMM UID/GID permit the same bounded job; supervisor authority is recorded separately. | Direct applied-state observations, not flags/start status; no entirely root-free jailer claim. [F02] [F03] [F10] |
| P0 | U08 | TM-DOS-01, TM-TIME-01 | Bounded CPU/timer/IO pressure stays within admitted total/rate ceilings and completes limit-triggered stop by its bound; workers counted externally. | Below-cap work completes with correct remaining budget. | PR-12; counter values alone are not enforcement. [F02] [N09] |
| P0 | U09 | TM-DOS-01, TM-DOS-02 | Small-cap bounded allocation produces defined job-only rejection/stop; independent peak charges and actual saturated recovery remain within limits/bounds. | Below-cap allocation and legitimate control work complete. | PR-13/22; host reserve must actually be usable. [F02] [N09] |
| P0 | U10 | TM-DOS-01, TM-DOS-02 | Small writable disk/log caps reject excess before host exhaustion/partial publish; bytes/inodes/retained debt stay capped across retry. | Below-cap disk/log output publishes wholly. | PR-14/15; no capacity reset with a new attempt ID. [F02] [N09] |
| P0 | U11 | TM-NET-01, TM-DOS-01 | Bounded excess network/IO stays inside defined burst/rate/work envelopes without exhausting independent control service. | Below-cap authorized transfer completes with exact bytes. | Rate throttling is not address policy or disk capacity. [F01] [N09] |
| P0 | U12 | TM-DOS-01 | On the disposable qualification host, the trusted harness creates a bounded host-side process/thread fixture tree inside the job's host cgroup alongside its VMM/helpers. Creation beyond the low fixture task cap rejects, no helper escapes accounting, and independent stop completes within its bound; an unqualified strict guest process-count requirement refuses. | The same host-side fixture below the cap completes, and actual independent stop remains usable; a separate below-budget guest workload runs within outer CPU/memory bounds. | Host task inventory and pids-controller rejection are observed directly. Guest process pressure alone does not establish host PID growth or test the host task cap. [N09] [G05] [F01] |
| P0 | U21 | TM-RES-01, TM-RES-02, TM-DATA-05 | False success claims, malformed paths/metadata and excess output cause no unsafe publication, host execution/discovery or unbound verified-success claim. | Exact authorized bounded output/complete dependency manifest publishes once as host-reported data. | PR-18/19/20; sample only, full interpreter inventory required. [G11] [K07] |
| P0 | U25 | TM-RES-03, TM-OPS-01 | Synthetic dishonest-host alteration cannot obtain a host-independent truth/attestation label merely from a valid host report. | Honest host report has bound provenance and visibly limited evidence level. | Limitation/refusal test; no malicious-host protection qualified. [K07] [A05] |
| P1 | U18 | TM-ISO-01, TM-AUTH-01 | Unsupported compiler/debugger/nested-container requirements refuse without native/privileged/shared fallback. | Representative supported build/test executes with independently observed backend identity. | Qualify actual offered toolchain, not generic Linux equivalence. [G09] [K03] [F01] |
| P1 | U19 | TM-ISO-01, TM-QUAL-01 | Absent KVM/nested features or unverifiable parent topology rejects before payload launch; no TCG fallback. | Matched bare-metal configuration launches the same admitted image. | Initial class is bare-metal; composed class remains closed. [N14] [F09] [Q01] |
| P1 | U23 | TM-DOS-01, TM-DOS-02 | Admission above one-host allocation or measured reserve refuses; under bounded pressure actual valid stop/revoke/control actions meet original deadlines. | Single-job cold/steady cohorts and other-principal control work complete; record full lifecycle percentiles. | No published boot microbenchmark substitutes for whole-service SLO. [F02] [B01] |

P0 means safety and lifecycle entry gates; all P1 gates also pass before offering
real build jobs. Priority orders work, not a waiver of any required property.
In addition, PR-05 must observe no overlapping foreign allocation under simultaneous
admission; a single admitted job is its positive control. PR-09/10/16/21/23 must
exercise real credential absence, authority/time, durable identity and revocation
at their owning services. A backend access probe cannot stand in for those gates.

U16 is deferred with concurrent-job sharing, not treated as proven by exclusivity.
U17 is deferred with snapshot/warm-clone support, while U04 must reject it now.
U22 is mandatory before either deferred Mac class opens: unsupported guest/toolchain
pairs refuse without fallback; the exact supported Linux or macOS/Xcode control
must run in its guest. Mac quota-slot recovery and all relevant P0/P1 gates repeat
on that separate tuple. [A02] [A09] [A10]

No U-item proves unknown escapes absent or all hardware side channels eliminated.
The full CIP direct observables, EIC exact-entrypoint/tuple gates and TC-25 inventory
remain mandatory. No private-path image parser, result unpacker or telemetry
consumer may be left outside that inventory because the chosen VMM is small.

## Owner decisions required

Each decision must be recorded before dependent qualification or activation.
Recommended answers are a starting policy, not approval already obtained.
They preserve all non-waivable secret, dedicated-host, external-enforcement,
lifecycle and qualification requirements of the pool CIP.

| Decision | Options | Recommended answer | Threat trace and evidence | Required decision record |
| --- | --- | --- | --- | --- |
| D-01 | Ordinary Linux guest-kernel VM; or separately scoped Sentry including KVM | Direct Firecracker first for the guest-kernel class, no automatic fallback. Reconsider on concrete workload or matched evidence; no security ranking. | TM-ISO-01, TM-AUTH-01; [F01] [G02] | State whether ordinary Linux guest-kernel semantics are required or Sentry/KVM is acceptable; approve representative initial workloads. This selects a boundary class, not a security ranking; gVisor/KVM also uses hardware virtualization. Resolve any change to the strict K4 timing contract through the explicit owner options above. |
| D-02 | One job per physical host; or concurrent shared class with accepted leakage | One job per physical host including parent layers. Defer sharing; explicitly disclose that exclusivity does not eliminate advanced side channels. | TM-SIDE-01, TM-DATA-03; [F02] [N14] | Record physical topology without foreign tenants, SMT/KSM/microcode and mitigation policy, drift handling and residual side-channel limits. |
| D-03 | Linux first; Linux-on-Mac; macOS/Xcode service | Linux first. Defer both Mac classes until quotas/privileges and applicable service rights are resolved independently. | TM-QUAL-01, TM-OPS-01; [A04] [A05] | Decide Linux-on-Mac and macOS/Xcode separately. Resolve the actual Tahoe SLA/service agreement and service model, not just VM-count allowance. |
| D-04 | Bare-metal qualification; or nested/composed deployment | Start bare-metal with known CPU/firmware, KVM and supported pinned kernel; nested support waits for parent/co-tenancy and full-layer qualification. | TM-ISO-01, TM-SIDE-01; [F09] [N14] | Name machine class, CPU, firmware, exact host kernel/configuration and page size. Nesting is deferred as a separate composed tuple, not banned forever. |
| D-05 | Scoped root setup/stop/cleanup plus non-root VMM; or entirely root-free operation | Accept only explicit bounded jailer/supervisor root duties and a per-job VMM UID. If zero root is mandatory, reject this proposed tuple and reopen the choice. | TM-ISO-02, TM-DOS-01; [F03] [F10] | Inventory privileged setup, observation, stop and cleanup duties, path and descriptor owners, and VMM UID/GID. The upstream minimal capability set remains undetermined; mandatory zero root reopens the choice. Record the K4 thread inventory, ordering evidence and any explicitly chosen alternative timing contract; no option silently opens stock cold boot. |
| D-06 | Supply disposable representative Linux test host now; or leave activation closed | Qualification owner supplies a dedicated disposable host and independent observability; name test/review owners. Separate Apple hardware only after D-03. No use of an agent or production service host. | TM-QUAL-01, TM-DOS-01; [F02] [A05] | Supply a disposable representative host, external observer and enforcer, named test owners and independent acceptance authority. Neither an agent host nor a production service host is a test target. |
| D-07 | Scoped dependency/transfer gateway; or broader reviewed internet policy | Default-deny upstream gateway, exact approved dependencies/destinations and bounded transfers; no generic internet or platform routes. Pin DNS/redirect behavior and failure mode. | TM-NET-01, TM-NET-02, TM-CRED-01; [F02] [G04] | Specify independent external gateway destinations, DNS/redirect/address-family rules, host-origin traffic coverage and fail-closed behavior on enforcer failure. |
| D-08 | Defined successor-visible state absence with explicit exclusions; or stronger forensic sanitization | Secret-free, non-sensitive workloads only; require actual qualified state removal/reset plus authorized external retention. Promise no physical secure erasure; stronger requirements remain unavailable until separately proved. | TM-DATA-01, TM-CRED-02, TM-OPS-01; [F02] [F05] | Inventory all successor-visible state including swap, logs, dumps and image seeds. Suspected compromise requires external fencing and independently authorized trusted re-enrollment, not local cleanup claims. |
| D-09 | Small fixed pilot budgets/deadlines; or variable workload classes | Use the small sizing fixture to measure whole-job charges, then publish exact hard caps and original stop/cleanup/revoke bounds with recovery reserve before admission. Numbers here are not approved service quotas. | TM-DOS-01, TM-DOS-02, TM-TIME-01; [F02] [N09] | Define rate, cumulative and peak budgets, permitted mechanism overshoot within admitted caps, kernel/helper charging and usable recovery reserve. Bind the CIP-0026 detection-to-revocation baseline of at most 300 seconds or its exact current bounded owner exception. |
| D-10 | Host-reported evidence only with independent fact/qualification approval; or stronger verification service | Host-reported level initially. Name the independent fact-check and activation authority; wait for EIC/CIP rollout prerequisites, complete exact-tuple evidence and reviewed carriers. No truth claim or owner waiver of unknown required guarantees. | TM-RES-03, TM-QUAL-01, TM-QUAL-02; [K07] [F09] | Bind published CIP-0025/CIP-0026 revisions, qualification, activation and revocation authorities. Define the independently controlled physical-stop mechanism and observer promised under AD-04; absent proof leaves completion unknown and reuse closed. |

The decisions above are also appended to [UNRESOLVED_QUESTIONS.md](../UNRESOLVED_QUESTIONS.md). The activation
record must include their resolved values, the independent fact-check disposition,
exact tuple, per-guarantee evidence and limitations, all required completed test
observations and review, and current revocation status. Until then the recommendation
is a qualification target only; no public job is admitted on its strength.
