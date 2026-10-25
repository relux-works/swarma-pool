# Pool host admission and qualification

Status: Draft (2026-10-25).

No host, class, public matrix row or job is activated.
This is a pool profile extension of the existing host qualification protocol.
It does not implement a kit, catalogue, signature format or admission service.
MUST, MUST NOT and MAY describe requirements for an eventual implementation.

## 1. Authority, inputs and limits

The [host qualification protocol][protocol] owns the owner-run procedure, exact
class identity, report/record boundaries, signing, submission and renewal. Its
[verified-platforms format][verified] owns the public evidence index. This profile
adds pool dimensions and obligations at those extension points. It MUST NOT
create another installation protocol, exporter authority or matrix trust root.

The following immutable inputs define this proposal's review basis:

| Input | Revision / scope |
|---|---|
| [Host protocol][protocol] and [verified-platforms format][verified] | [`5a193574575bd0a4681a0d5fe70dc15792bc29f8`][protocol]; proposal, no implemented generic kit or verified rows |
| [Shared guarantee registry](https://github.com/relux-works/swarma-user-manager/blob/71dd888e23350d736ea74589ea1f5b4692258a2d/spec/platform-parity.md) | [`71dd888e23350d736ea74589ea1f5b4692258a2d`](https://github.com/relux-works/swarma-user-manager/blob/71dd888e23350d736ea74589ea1f5b4692258a2d/spec/platform-parity.md); all 42 IDs and the unchanged four statuses |
| Foreign-code execution CIP, CIP-0026 | [`88b0d8615a5df8728e0e9540a9edd708c0ca5660`](https://github.com/relux-works/curator-spec/blob/88b0d8615a5df8728e0e9540a9edd708c0ca5660/cips/CIP-0026-foreign-code-execution.md), `cips/CIP-0026-foreign-code-execution.md`; PR-01–PR-23, FX-01–FX-26, CO-01–CO-22; security revocation ≤ 300 seconds from detection unless its exact current bounded owner exception applies |
| Execution Isolation Contract (EIC), CIP-0025 | [`dea17185c0317892c13dda823baaeded684f6352`](https://github.com/relux-works/curator-spec/blob/dea17185c0317892c13dda823baaeded684f6352/cips/CIP-0025-execution-isolation-contract.md), `cips/CIP-0025-execution-isolation-contract.md`; published invocation, bindings, receipts, qualification and weaker acceptance; publication is not activation |
| [Runner hosts](runner-hosts.md) | RH-17 binds complete thread ordering and restrictive-filter identities, with stock main-filter-after-resume admission closed; all RH-T01–RH-T30 retained |
| [Backend choice](../research/backend-choice.md) | Provisional choice; 22 prioritized U-cases and D-01–D-10 records including explicit unresolved K4 owner options |
| [Threat model](../research/threat-model.md) and [backend evidence](../research/isolation-backends.md) | Threat model and backend evidence retain original source IDs plus two dated primary additions |

Changed companion requirements MUST be reconciled by exact revision before
activation; documentary acceptance and CIP publication do not activate a deployment.
The backend choice's comparison groups now use the published CIP identifier map;
their backend-contribution annotations MUST NOT become deployment-qualified statuses.
The companion specifications remain subject to independent acceptance.

The initial target is Firecracker on dedicated bare-metal Linux/KVM, one fresh
cold VM per job and one live job per physical host. This is conditional on owner
decisions, independent fact checking and qualification. Native execution, TCG,
shared jobs, snapshots/warm clones, nesting, GPUs and both deferred Mac classes
MUST NOT be selected as fallbacks. Their unsupported requests must refuse.

Records remain host self-reports under the local hardware/kernel/launcher TCB.
An independent reviewer can check provenance, reproduce authorized observations
and enforce external controls; a signature alone proves neither physical host
identity, truthful computation, completed erasure nor resistance to root compromise.
Advanced side-channel elimination is `absent`. Physical attacks and unknown
escapes remain residual risks; exclusive scheduling does not eliminate them.
For host containment against hostile guest root the guest kernel is untrusted;
any guest-internal enforcement claim MUST separately disclose its kernel/agent
trust assumptions. Under AD-04 local stop/health/cleanup assertions MUST NOT prove
physical completion. External admission/network fences MUST remain effective;
a promised physical-stop bound requires an independently controlled mechanism and
observer outside the compromised host TCB. Without that evidence completion MUST
remain unknown and reuse prohibited. Separately authorized recovery/re-enrollment
MUST NOT retroactively discharge a missed deadline.

## 2. Pool class extension

A pool class MUST use the protocol's canonical versioned descriptor and class
hash, including every base dimension and the additional dimensions below. The
research catalogue owner must approve their encoding and dependencies before use.
These logical fields are a proposal, not new members of an existing closed codec.
Unknown required fields refuse; known inapplicable fields require an explicit
qualified value. Product names, mutable tags and a matching family are insufficient.

| Field | Exact class-defining value | Why a change creates a new class |
|---|---|---|
| PC-01 Host OS/kernel | Distribution/build, exact kernel binary/configuration/features, update profile and page size | Changes host enforcement, accounting and supported VMM assumptions |
| PC-02 CPU | Executed architecture/ABI, CPU vendor/model/stepping and relevant feature/security mask | Changes instruction, virtualization and side-channel assumptions |
| PC-03 Firmware | CPU microcode and relevant firmware/boot/management-controller versions and security settings; exact CPU mitigation policy, SMT and memory-deduplication/KSM state, required checks and drift policy | Changes substrate integrity, persistence and mitigation assumptions |
| PC-04 KVM | Exact KVM implementation/build/configuration, required capabilities and availability | Changes the hardware boundary and supported device/VM behavior |
| PC-05 Outer topology | Physical versus virtual substrate, nesting disabled/enabled, every outer enforcing layer and sharing policy | An outer hypervisor or undisclosed co-tenant changes the whole boundary |
| PC-06 VMM | Exact release, executable digest, build/features, dependency manifest and enabled devices; approved production build target, exact restrictive seccomp filter identities, explicit inventory of all execution and guest-facing threads with creation/lifetime/filter paths, and installation-order policy | Changes guest/host attack surface and lifecycle/resource semantics |
| PC-07 Jailer/helpers | Jailer and helper digests/versions, launcher service/UID policy, privilege operations and drop sequence; fresh per-job VMM UID/GID policy distinct from supervisor setup/observation/stop/cleanup authority, with path/descriptor owners | Changes setup authority, inherited interfaces and helper accounting |
| PC-08 Guest kernel | Kernel digest, configuration, boot arguments and module policy | Changes admitted workload behavior and assumptions used by tests |
| PC-09 Runner image | Approved root-image/init/toolchain manifest digests, provenance policy and immutable base policy; acceptance policy for embedded machine/application identities, random seeds, credentials, prior workload state, revocation, early-boot entropy readiness and per-execution identity | Changes executable environment and materialization inputs |
| PC-10 Invocation/boundary | Profile digest, production entry, environment/FD policy, readable/writable roots, mounts and IPC purposes; exact pre-API versus pre-guest phases, reviewed source-to-binary ordering provenance or qualified equivalent pre-execution boundary, protected launch/filter bindings and corroborating observation policy | Changes first-instruction protection and transitive configuration |
| PC-11 Network enforcement | Logical enforcement point/layer, implementation/version, upstream topology, resolver/proxy/policy digests, failure behavior | Changes independent containment, identity binding and available routes |
| PC-12 Resource envelope | CPU rate/time, elapsed work, memory, host tasks, disk/inodes/I/O/network/output caps, measurement and protected usable reserve; required cgroup controllers/effective-limit and complete charged-scope policy, allowed scheduling classes, actual kernel/helper charging, measured enforcement/overshoot envelope and headroom within admitted caps | Changes hard/soft scope, response bounds and control availability |
| PC-13 Storage | Filesystem/driver, mounts, backing/encryption/swap/dump profile, sharing and durability assumptions | Changes visibility, accounting, crash and residue behavior |
| PC-14 Disposal/devices | Device allowlist, reset/sanitize methods/versions, nine-class cleanup procedures and evidence policy; AD-04 independent physical-stop mechanism and observer, external fences, original completion bounds and evidence policy for separately authorized trusted re-enrollment | Changes whether absence, reset and safe reuse can actually be established |
| PC-15 Native dependencies | Service manager/issuer, libc/loader, directory stack and security module/policy versions, as required by the base descriptor | Changes privileged service behavior and local qualification assumptions |
| PC-16 Pool services | Gateway/admission/receipt/intake/custody/revocation protocol and enforcement versions, dependency profiles and purpose scopes | Changes external authorization, completion, freshness and result guarantees |
| PC-17 Qualification | Pool profile/registry/catalogue revisions, independent kit release/report version, adapters/toolchain, reviewed source/build/filter provenance and ordering-evidence policy, clocks and published lifecycle bounds | Changes accepted evidence, required coverage or admission contract |

Any differing class field requires a new class, even for a patch version or an
image-only update. Compatibility rules may reuse test definitions/selection;
they MUST NOT merge unequal descriptors or inherit passing results. All applicable
obligations require fresh results on each host. New mechanisms/interactions need
new tests before claims can be upgraded. A pool class inherits base fields; omission
of an apparently station-specific dimension cannot silently remove a dependency.

Class descriptors MUST exclude hostnames, addresses, serials, owner/machine IDs,
keys and timestamps. PC-05/PC-11 describe topology and enforcement roles, not private
locations. Private unique inputs stay under owner custody with sanitized digest
bindings. The enrolled host subject and generation belong to host records, not
the class hash. Two equal classes never authorize one host to use another's record.

PC-03 mandatory mitigation state MUST be known and current before placement;
unknown or changed state closes admission. Per-execution applied cgroup membership,
controllers, effective limits and relevant-thread seccomp observations belong in
protected host/attempt evidence bound to the class policy, not host-specific paths
or process IDs in the class hash. A process-start reply, requested flags or absence
of a seccomp-disabling flag MUST NOT substitute for applied-state evidence.
Post-start thread status only corroborates reviewed ordering; `/proc` `Seccomp`
mode and `Seccomp_filters` count MUST NOT establish filter identity or pre-guest
installation. Debug builds, `--no-seccomp`, empty required or allow-default
filters and unsupported-target default-policy fallbacks MUST refuse admission,
regardless of successful filter-application return or process/API start.
Cgroup CPU bandwidth MUST NOT be treated as a universal limit on arbitrary
scheduling classes; documented `memory.max` transient overshoot MUST fit the
measured envelope/headroom inside admitted caps without consuming recovery reserve.
Unknown enforcement refuses rather than publishing a hard-limit claim. Cold boot
MUST NOT sanitize an image by assertion, and small-cohort identity uniqueness
MUST NOT be labelled proof of randomness.

## 3. Owners, identities and ordered admission

The owner runs and reviews the generated kit. The research catalogue owner versions
tests, status criteria and extensions; kit maintainers own collection adapters
used by the owner-run kit. An independent pool qualification authority reviews the exact
production tuple. Enrollment, matrix publication and final placement retain their
separate authorities. The owner cannot self-approve pool serving by signing a run.

A dedicated export key MUST be owner-approved and separately registered for one
host's qualification records only. Use the protocol's closed, domain-separated
Ed25519 envelope once approved, with exact canonical bytes and published vectors.
Export authority grants no pool enrollment, job launch, platform or publisher power.
Hardware binding requires separately qualified evidence; software custody must be
labelled as clonable self-report provenance. No arbitrary self-signed key is trusted.

Pool enrollment follows RH-03–RH-06: independent provisioning approval and fresh
host-key proof bind a pool-only identity to the exact host/boot/generation externally.
It is unavailable to guests and authorizes only approved pool operations. Export
and pool-operation keys MUST have separate purpose/custody policies and MUST NOT
be implicitly interchangeable. Their exact carriers remain activation dependencies.
No platform credential, agent messaging identity, broker lease or subscription
binding may enter the host/image/backups or guest. Enrollment does not mean serving.

All protocol prerequisites remain mandatory in this ordered extension:

| Step / existing protocol phase | Pool addition and evidence | Decision / safe state |
|---|---|---|
| PA-01 Read-only survey | Inventory complete class, platform-secret absence locations, physical topology, available KVM and independent network/cleanup/revocation dependencies; protected unknowns explicitly scheduled | Kit adapter records observations; missing immutable required facts prevents a reviewable install plan |
| PA-02 Generated kit | Pin PC-01–PC-17, complete owned change set, provenance, independent report/catalogue versions, live fixture inventory, cleanup/evidence paths and recovery plan | Kit generator produces reviewable bytes; no per-host hand patch or unpinned download during apply |
| PA-03 Owner review and registration | Owner approves exact kit/effects/host/reference/window and separate sanitized disclosure; external authority approves provisioning and registers export identity; pool identity may enroll in pending-only state | Independent enrollment decisions recorded externally; no public serving follows from owner approval |
| PA-04 Protected stage | Use protocol's external root-writer exclusion, closed admission and authenticated bootstrap; stage approved bytes in owned protected locations | Owner stages only; partial stage preserves fences and recovery ledger |
| PA-05 Apply | Preserve exclusion/fences across lease handoffs, execute reviewed initial/pin/native boot issuer; activate independent upstream deny before any test workload | Owner/issuer checks all required local and pool observations; drift refuses, no guessed pins or unrelated state mutation |
| PA-06 Verify | Verify installed bytes, current boot/latest successful issuer and durable completion; fixture evidence proves secret absence, live network denial, working whole-content custody/cleanup and reachable fresh revocation. Pre-API observations verify approved production build/filter identities and actual cgroup/controllers/effective limits/charged scope. The complete execution/guest-facing thread inventory and actual restrictive-filter installation before any guest instruction must be established by reviewed source-to-binary provenance with protected launch bindings or a separately qualified equivalent pre-execution boundary; post-start mode/count observes neither ordering nor identity. Image-state/entropy and mitigation acceptance remain explicit; stock cold boot resumes vCPUs before the main VMM filter and cannot meet this gate. | Qualified issuer produces bounded report; missing, stale or unknown checks cannot produce success |
| PA-07 Stop after verify | Stop exactly as protocol requires; preserve necessary fences and evidence; verification grants no serving lease | Owner stops procedure; no script auto-enrolls as serving or opens public ingress |
| PA-08 Signed record | Owner-controlled exporter signs only sanitized completed run and report/evidence digests; sequence/predecessor and enrolled subject bind all versions and outcomes | Host exporter has own-host authority only; diagnostic/unrun records cannot be signed as qualified success |
| PA-09 Submission and matrix | Publisher independently verifies trust, signature, coverage, exact class, freshness, revocation and sanitized evidence without executing supplied code | Accepted exact-class row/record under approved policy; pending submission is unavailable |
| PA-10 Pool qualification acceptance | Independent authority checks production entry, all blocking catalogue results, external identity/network evidence, decisions and budget/service coverage against the same tuple | Explicit protected acceptance; neither host self-report nor a station row supplies this act |
| PA-11 Final serving/placement | External controller independently rechecks enrolled generation, current local qualification, accepted record, revocation, capacity/debt and no fence; atomically reserves the job/host. For a qualified entry point the launcher verifies actual cgroup state, approved production build/filter identities, image/entropy policy and mandatory mitigation state before InstanceStart; it binds the complete thread inventory and reviewed installation-order/identity evidence to the actual launch, or uses a separately qualified equivalent pre-execution boundary, ensuring all required filters precede any guest instruction. Post-start observation corroborates only. The stock main-filter-after-resume path remains pending and must refuse before allocation or InstanceStart. | Only a current exact tuple serves; repeat final checks at start/resume/restart and after asynchronous work |

Registration and bounded kit fixtures may occur while pending. They MUST NOT
become public serving or broaden credential scope. Catalogue analysis happens
only after evidence collection: it consumes the immutable versioned kit report
and referenced evidence, never host commands, provider queries or live discovery.
A missing report observation requires a new kit run/approved adapter, not a
catalogue-side probe. Catalogue and kit/report versions evolve independently.

Before PA-10, the evidence path must demonstrate actual blob retrieval outside
the destruction scope, lifecycle evidence after controller restart, all required
cleanup confirmations, and bounded valid stop/revoke under pressure. A path name,
successful write, connectivity ping or manifest digest does not establish this.
Before every PA-11 decision, stale/unreachable revocation or unresolved original
allocation/cleanup/custody debt MUST refuse new work and preserve external fences.

### 3.1 Required launch phases and current availability

The pre-API gate MUST verify actual cgroup/controllers/effective limits/charged
scope, admitted production build, exact restrictive filter identities and a complete
inventory of all execution and guest-facing threads. Include every vCPU, main VMM,
API when enabled, and helper/worker thread, with creation/lifetime, guest-facing role,
filter installation/inheritance path and failure handling. The distinct pre-guest
gate MUST establish that all filters required by the profile are actually installed
before any guest instruction. It MUST NOT demand live observations before thread
creation. Missing, mismatched, failed or unknown application or an omitted thread
MUST forbid release, not merely stop a guest after it ran. Later-created threads
MUST NOT introduce an unfiltered execution/guest-facing path.

Qualification MAY establish ordering by reviewed source-to-binary provenance:
review exact source, recipe/toolchain/target/features/dependencies, binary/filter
bytes, all thread/filter paths and fail-closed installation semantics; bind running
bytes, selected filters, attempt and policy through protected launch evidence under
the declared trusted-host/kernel assumptions. Alternatively qualify a protected
hold/verify/release interface or equivalent pre-execution boundary. No external
live observation is mandatory when reviewed provenance establishes the complete
order. Post-start thread status is corroborating evidence only: neither mode/count
nor a successful application return proves restrictive filter identity or prior
installation. These evidence methods and bindings require independent qualification
under PC-06/07/10/17 and [RH-17](runner-hosts.md#runner-lifecycle); AD-04 independent
stop/evidence duties remain unchanged.

At Firecracker v1.17.0 commit `95f868c8e345b1cc8faccd1a3c910b4989dc3f58`,
[each vCPU installs its filter before running](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/vstate/vcpu.rs#L217),
and installation failure panics with production process-abort semantics. But
[stock cold boot resumes vCPUs](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/builder.rs#L373)
before [the caller installs the main VMM filter](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/firecracker/src/api_server_adapter.rs#L222).
A guest may execute while that thread remains unfiltered; later all-thread status
cannot repair this ordering. JSON/no-API has the same limitation (source details
and empty/allow-default pitfalls are recorded in RH-17). This is not a claim of
unfiltered vCPU execution or unverifiable seccomp. Stock also provides no external
all-thread verify-then-release step. The strict guarantee remains **PENDING-VERIFY**
and external admission MUST refuse public placement before allocation or
`InstanceStart`; PA-06 MUST NOT emit qualified success, PA-10 MUST NOT accept the
tuple and PA-11 MUST NOT dispatch it. This remains visible in AG-11/13/18.

A launcher/patch installing the main filter before resume then verifying requires
new exact-profile qualification. Paused snapshot restore with `resume_vm=false`
is a different profile with snapshot trust, residual-state, identity and entropy
acceptance, outside this cold-only class. Post-start-only checking and the narrower
“every thread filtered before it handles guest-controlled input” contract require
explicit owner decisions under D-01/D-05, never silent substitution. The
[backend-choice owner options](../research/backend-choice.md#linux-primary-configuration-and-alternatives)
approve no replacement here. Refusal-only stock evidence cannot pass the missing
successful production-execution positive.

## 4. Proposed pool catalogue extension

This section is a proposal to the research catalogue owner, not a registered
catalogue release or implemented harness. PQ-T names below are stable proposal
references. The owner must version exact proof/status criteria, report inputs,
finite fixture limits, compatible report versions and dependencies before release.
All cases are currently **unrun**. No test sensitivity, runtime enforcement or
qualification is claimed by this document's structural validation.

All 40 cases are blocking for the initial profile, including unsupported-request
refusals. Live/root probes belong to the kit's authorized disposable test host
and approved adapters. Catalogue tests read only their reports/evidence. Synthetic
markers and small bounded fixtures replace real secrets/data. No production-host
fault injection, unsafe exhaustion or unreviewed downloaded code is authorized.

Each table row requires a directly observed negative and an actual permitted
operation through the same production entry/scope. Generic error, timeout, unchanged
write canary or signature validity cannot substitute for its oracle. Each controlled
narrowing is isolated to the stated property, with unrelated protections retained;
the named witness must fail for the intended reason. A surviving/skipped/crashed
control leaves that claim uncovered, never proved by structural row counts.

### 4.1 Identity, profile and enforcement cases

| Case / source obligation | Direct negative and forbidden effect | Actual nearby positive | Narrowing / named witness |
|---|---|---|---|
| PQ-T01; PR-09, RH-T01, EQ-19/23 | Synthetic forbidden platform key/binding in each host/image/backup/env/handle location rejects serving; broker binding/issue/renew effects stay zero | Secret-free image starts a bounded job without any broker client or subscription | Omit one location/issue gate independently; PlatformSecretAbsent / UntrustedBrokerDenied |
| PQ-T02; PR-10, RH-T02/03, EQ-20 | Guest UID/job token or valid pool peer requests platform/foreign-object/proxy purpose: no protected bytes, signer/broker/message/repository effect or onward connection; enrollment/export keys unreadable | Assigned pool object transfer works; authorized native test peer retains exactly its own rights | Remove purpose/read gate with valid peer/endpoint; PoolPurposeDenied / HostKeyReadDenied |
| PQ-T03; RH-T04/05, CO-18 | Self-enrollment, stale/copied generation and revoked/expired sessions cause zero admitted effect, including retry/async completion | Independently enrolled current generation performs its scoped action | Skip final independent scope/currentness check; EnrollmentBeforeEffect / RevokedSessionDenied |
| PQ-T04; PC fields, RH-T06/07, EQ-10/30, U19 | Differ any one class field, omit KVM or conceal outer topology: no shared class/accepted placement or fallback launch; unknown/drifted CPU mitigation, firmware/microcode, SMT or KSM state refuses before placement | Equal full descriptor hashes equally; qualified bare-metal tuple runs the admitted image; same exact recorded mandatory mitigation state permits the admitted placement | Omit one dimension at a time; ClassDimensionBound / NoBackendFallback; omit only mitigation-state recheck; MitigationStateCurrent |
| PQ-T05; PR-05/06, RH-T08/18, U23 | Concurrent reservations cannot overlap foreign execution anywhere in physical scope; side-channel-elimination request refuses with status absent | One exclusive job completes; reduced declaration discloses residual leakage without claiming elimination | Ignore one concurrent reservation or infer elimination from exclusivity; PhysicalExclusive / SideChannelRequirementDenied |
| PQ-T06; EQ-11/17, FX-25, CO-02 | At start/resume/restart, absent/pending requirement or weaker act with changed matrix/bounds/bindings, missing/future/expired interval refuses; no hidden reduction | Exactly bound currently valid independently authenticated weaker act admits only qualified reduced scope, disclosed in receipt/reply | Drop matrix binding or act-time check only; AcceptanceMatrixRevisionDenied / AcceptanceValidityDenied |
| PQ-T07; EQ-04/10/16/32, RH-T14, CO-03/04, U04 | Author flags/mounts/hooks/templates or incomplete boundary/receipt allow no first instruction, helper side effect or partial input publication. Before InstanceStart, cgroup-v2/no-cgroup-arguments/absent-parent successful start, wrong actual cgroup/controller/effective-limit/charged scope or unapproved build/filter identities refuses. Missing/mismatched/unknown required-filter installation or omitted main/API/vCPU/helper thread forbids the first guest instruction. Debug build, `--no-seccomp`, empty required/allow-default filter, unsupported-target fallback or `/proc` mode/count used as identity (same status but different or unknown filter bytes) rejects without guest release/partial effect. A post-start all-filter observation cannot pass main-filter-after-resume ordering. StockColdStartPendingDenied: otherwise valid pinned stock requests refuse before allocation/API because the main VMM filter follows vCPU resume and no equivalent pre-execution boundary is qualified. | Protected exact invocation and own output work only on a separately qualified entry point after immutable validation, pre-API actual cgroup/build/filter-policy verification, then all-required-thread filter installation before any guest instruction established by reviewed source-to-binary provenance or qualified equivalent pre-execution boundary; full applied boundary and durable receipt remain checked before guest release, with per-job VMM UID/GID and separate supervisor authority. Approved restrictive filters, complete thread inventory and protected launch bindings execute with matching status as corroboration; change each forbidden build/filter/identity/inventory/order input independently and require refusal. Post-start mode/count corroborates only. No stock execution positive is available; pending refusal cannot substitute. | Skip pending-path apply/receipt or configuration closure; BoundaryBeforeInstruction / InvocationClosed. Separately omit pre-API cgroup verification or pre-guest installation-order/identity verification on the qualified entry point; AppliedCgroupBeforeInstruction / AppliedSeccompBeforeInstruction must fail. Treating stock API success as the protected release is the StockColdStartPendingDenied counterexample. Independently permit debug build, disabling flag, allow-default/empty fallback, omitted thread, count-as-identity or post-start-as-order; ProductionBuildOnly / SeccompEnabled / RestrictiveFilterIdentity / ThreadInventoryComplete / FilterCountNotIdentity / FilterOrderBeforeGuest must fail respectively. Runtime controls remain unrun. |
| PQ-T08; PR-01, RH-T13, EQ-20, U02 | Direct/link/mount/global-temp/FD reads return no distinct host or live-neighbour marker in outputs/logs/artifacts; live neighbour belongs to isolated boundary fixture | Own input returns exact bytes through the same path | Remove read denial only, preserve writes; HostReadDenied / LiveNeighbourReadDenied |
| PQ-T09; PR-02/17, RH-T14/18, U03 | Forbidden writes/rename leave protected host/peer/base bytes and metadata unchanged after restart; writable base never admitted; digest-valid root image with unaccepted embedded machine/application identities, random seeds, credentials or prior workload state rejects; immutable/cold alone is insufficient | Private output writes/readback work; approved immutable base resolves exact bytes; accepted sanitized read-only image and private job state meet identity policy | Remove write/base-immutability gate only; HostWriteDenied / InputImmutable; omit only image-state acceptance; ImageStateAccepted |
| PQ-T10; PR-03, RH-T15, EQ-20, U01 | Actual FD/namespace inventory contains no undeclared interface; forbidden socket/service purpose yields no response/content/effect/spawn/onward request even with valid peer | Declared guest I/O and exact allowed service operation complete | Remove handle or service-purpose check separately; DescriptorSetClosed / ServicePurposeDenied |
| PQ-T11; PR-17, RH-T16, EQ-28, U05 | Unauthorized input/digest or malformed external-reference image causes no foreign read, outside-root write, hook/discovery, partial publication or launch; revoked image or unqualified early-boot entropy policy rejects before admitted execution | Approved supported image/input fully materializes exact bytes with authorization; current accepted image meets exact guest-stack early-boot entropy policy and per-execution identity; duplicate-free small cohort is a regression observation only | Skip entitlement or image-reference validation separately; InputAuthorized / ImageMaterializationSafe; omit revocation or entropy-readiness gate separately; ImageRevocationCurrent / EarlyEntropyReady |
| PQ-T12; PR-18/20, RH-T16/25, EQ-28/29, U21 | Every inventoried unpacker/parser/renderer/telemetry consumer rejects unsafe types/links/metadata within finite work; no outside write, execution/discovery or partial import | Benign whole object materializes or is intentionally opaque; inert output remains inspectable | Omit each interpreter's confinement/work gate; InterpreterConfined / ReturnedDataInert |
| PQ-T13; PR-07, RH-T22, EQ-18, U06/07 | Independent upstream capture sees zero denied host/LAN/metadata/peer/platform traffic, including host-origin after-escape fixture routes and both families | Scoped dependency/transfer reaches only allowed endpoint with exact bytes | Bypass one outer/source/destination enforcement layer; UpstreamDestinationDenied |
| PQ-T14; PR-08, RH-T22, FX-12 | Unsolicited/wrong-source ingress and wrong-object transfers establish no job connection, content release or accepted control effect | Current authenticated exact-scope transfer completes | Remove ingress/object authorization only; IngressTransferDenied |
| PQ-T15; RH-T23, CO-10, EQ-18, U06 | DNS answer, redirect, protocol/family, resolver bypass or proxy failure sends no denied packet; old routes/sessions actually close before next effect and by B-network | Allowed chain works; transient failure closes paths in time and fresh policy permits own transfer | Omit stuck route/session closure, keep admission deny; RouteClosedBeforeEffect / NetworkIsolationByBound |
| PQ-T16; PR-12, RH-T19, EQ-25/26, U08 | Aggregate CPU rate/time and elapsed work exceed no admitted hard envelope; retry/reboot/guest clock cannot extend original budget; expiry stop actually completes; disallowed scheduling class or uncharged attributable host-kernel/helper work cannot bypass the admitted aggregate CPU envelope | Below-cap workload completes with correct remaining budget; bounded pressure still permits actual stop; each allowed scheduling class and complete actual worker placement/charging passes below its budget | Remove cumulative/elapsed enforcement or unknown-path stop deadline; CpuWorkBound / ExpiryStoppedByBound; omit scheduling-class or charged-worker gate separately; SchedulingScopeCharged |
| PQ-T17; PR-13/22, RH-T19, U09 | Charged whole-unit peak memory stays within hard ceiling; saturation cannot consume protected usable recovery capacity; measured memory.max transient overshoot outside admitted hard cap or into protected reserve fails even if nominal limit is configured | Below-cap allocation and real stop/revoke/control actions complete under bounded saturation; within-headroom memory pressure fits the published measured envelope and actual recovery completes | Keep nominal reserve, omit effective protection; MemoryCeiling / SaturatedRecoveryByBound; omit measured envelope/headroom check only; MemoryEnvelopeWithinCap |
| PQ-T18; RH-T19/29, U12/20 | Trusted bounded host-side process/thread fixture in job cgroup alongside VMM/helpers rejects creation above task cap; helpers never escape accounting; missing setup authority refuses before effects | Below-cap host fixture completes and independent stop works; separate below-budget guest runs | Omit host pids or helper membership gate; HostTasksBound / WholeUnitAccounted |
| PQ-T19; PR-14, RH-T19, EQ-26, U10/11 | Actual bytes/inodes/I/O/network bursts and retained debt never exceed admitted caps across retry/identity changes; no host exhaustion/partial publication | Below-cap storage and transfer complete with exact bytes and prior debt charged | Drop one cap or retained-debt charge independently; StorageIoBound / DebtBeforeAllocation |
| PQ-T20; PR-15/22, RH-T19/25, U10/21/23 | Log/artifact/expansion/parser and queue/work caps hold; excess load cannot starve other-principal valid control or original stop/revoke deadlines | Bounded whole output publishes and other-principal valid recovery completes during saturation | Drop parser cap or actual saturated recovery gate; OutputWorkBound / IndependentRecoveryByBound |

### 4.2 Completion, records and recovery cases

| Case / source obligation | Direct negative and forbidden effect | Actual nearby positive | Narrowing / named witness |
|---|---|---|---|
| PQ-T21; PR-11, RH-T17, EQ-07/09, U13 | Leader exit or unknown scan cannot prove absence: whole execution-account work, indirect/OS-spawned workers and helpers must reach zero throughout quiet period by original B-stop; stale stop never signals successor | Ordinary cancellation and transient-unknown in-flight launch resolve to actual full stop in time | Omit stuck/unknown full-scope deadline only, retaining fences; WholeJobStoppedByBound |
| PQ-T22; PR-04, RH-T20, U15 | Seed each of nine resource classes: missing reset/removal, old bytes/mapping/grant or incomplete confirmation at B-destroy fails cleanup and denies all host reuse; under AD-04 host-signed cleanup/zero alone cannot authorize reuse while independent stop/recovery evidence is missing | Actual inventory/reset complete in time; successor reads own bytes, old grants reject and retained predecessor output remains only in authorized external custody; external completion plus separately authorized trusted recovery evidence permits only fresh enrollment/qualification | Skip stuck disposal separately per class, keep quarantine; ResourceClassDestroyedByBound; trust local cleanup for reuse only; IndependentRecoveryBeforeReuse |
| PQ-T23; PR-21, RH-T12/21, EQ-09/23/34, U14 | Interrupt each allocation/start/observe/collect/disposal checkpoint: no duplicate start, stale signal, fabricated completion, debt loss or clean/reuse on unknown | Original intact effect/manifest actually reconciles before dependent release/reuse; disposal completes by original B-destroy | Omit unknown original-effect gate or cleanup completion separately; OriginalEffectBeforeDependentAction / ManifestCleanupByBound |
| PQ-T24; RH-T24, EQ-29, CO-13 | Collection outside the qualified fenced/quiescent point cannot publish a complete result; missing blob, digest-only receipt or in-scope content forbids deletion/retirement; obligation retained | At the qualified collection point every full blob is durably retrievable outside deletion scope before deletion, or exact independently authorized per-act discard is recorded | Skip collection-point or pending-content prerequisite separately; CollectionPointQualified / ContentBeforeDeletion |
| PQ-T25; RH-T25, CO-14, EQ-29, U21 | Invalid/missing provenance never wholly publishes; valid complete accepted intake still unknown/fenced past B-intake fails completion, independently of quarantine | Valid bounded result wholly publishes by original bound; invalid sibling reaches intended terminal refusal with no partial import | Drop stuck-path completion only, keep quarantine; IntakeCompletedByBound |
| PQ-T26; PR-19, RH-T26, FX-17/26, U25 | Missing dependency, wrong request/attempt/image/output bytes or fabricated host success/cleanup cannot yield bound verified-success or host-independent truth/attestation label; AD-04 local signed stop/zero/healthy reports cannot satisfy an independent physical-stop claim | Exact complete result publishes once with all actual runtime dependency digests and visible host-reported evidence level; independent mechanism/observer evidence supports only its qualified physical stop scope | Drop dependency or evidence-level check separately; ResultManifestBound / HostClaimNotAuthority; promote local stop claim only, retain external fences; IndependentStopEvidenceRequired |
| PQ-T27; RH-T27, CO-20 | Covered artifact/log/backup bytes remaining after expiry + B-delete fail actual deletion even when retrieval denies; no unapproved audit-payload exception or lost obligation | Authorized pre-expiry retrieval works; all covered copies are removed with evidence by original bound | Keep access denial, omit stuck deletion completion; CoveredCopiesDeletedByBound |
| PQ-T28; RH-T28/29/30, EQ-03/20 | Job author shell/debug/template/device requests cause zero host privilege effect; unauthorized admin or missing actor/generation/reason creates no session or retained-byte access | Typed admitted host action and current audited pool administration complete only within declared scope | Drop privilege closure or audit authorization independently; HostPrivilegeDenied / AuditedPoolAdminOnly |
| PQ-T29; RH-T09, CO-06/11/13 | At accepted drain, new placement stops; live/unknown work or incomplete cleanup/custody at B-drain fails completion even if queue empty/fenced | Actual stop, full cleanup and custody/disposition finish by original drain bound, including transient unknown | Retain placement fence, omit stuck drain completion; DrainCompletedByBound |
| PQ-T30; PR-23, RH-T10, EQ-30/31, CO-08/19 | Revoke blocks new starts/releases immediately; missing actual stop or confirmed separately qualified non-revoked narrowing at B-revoke fails; host clean report/new guest cannot clear incident fence; absent independent physical-stop evidence under AD-04 remains unknown, fails required bound and prohibits reuse despite local healthy/cleanup reports; re-enrollment cannot retroactively pass a missed deadline | Actual disposition in bound; independent trusted rebuild, fresh enrollment/qualification permit only new tuple; approved independent stop mechanism and observer establish actual completion in the original bound; separately authorized recovery admits only a fresh generation | Keep fences, drop stuck disposition or rebuild gate separately; RevokeDispositionByBound / RebuildBeforeReadmit; accept local stop as independent only; IndependentStopEvidenceRequired |
| PQ-T31; RH-T11, CO-20 | Retirement still needs access/identity revocation, approved disposal and retained-obligation disposition by B-retire; fenced/unknown persistent compromise cannot be called retired | Actual complete retirement by original bound with external evidence; replacement qualifies separately | Keep fence, omit unknown disposal completion; RetirementCompletedByBound |
| PQ-T32; PR-16, EQ-13/14/24, CO-18 | Rollback/reboot/retry/receipt refresh cannot extend authority, operator-act validity, original deadlines or budgets; missing current external time refuses dependent effect | Fresh exact authority permits bounded action under original clock; delayed current observation does not reset it | Drop unknown-path original-clock/currentness check; AuthorityTimeCurrent |
| PQ-T33; protocol §1/6 | Forged/noncanonical/duplicate/oversized envelope, wrong domain, foreign subject, unregistered/revoked key or conflicting sequence never yields accepted record; exporter cannot affect another host/platform/publisher | Own enrolled host's exact approved canonical record verifies; identical resubmission is idempotent without freshness extension | Omit one trust/purpose/parse/sequence binding; HostRecordScoped / RecordReplayDenied |
| PQ-T34; protocol §1/3 | Survey/apply drift, incomplete added/changed/removed delta, unknown protected facts, partial stage/apply/issuer/verify or unowned recovery prevents completion and serving; never silent repin | Complete reviewed protected run and all native current-boot checks yield one durable completion, then stop without serving | Omit one delta/drift or pending completion gate; ReviewedKitBound / PartialRunFenced / VerifyDoesNotAdmit |
| PQ-T35; protocol §7, verified row contract | Omitted shared/local ID, undefined proof criteria, expired evidence, mixed-record best cells or hidden compensation cannot publish a current claim or admit required absent/pending property | One complete consistent accepted record supports the full exact map with visible limits; legitimate constrained class remains labelled | Drop one map/evidence/status gate; MatrixWholeMap / StatusProofDefined / WeakerVisible |
| PQ-T36; protocol §4.1 | Catalogue host/provider query, unsupported kit report, changed evidence bytes or catalogue/kit version conflation refuses evaluation; no probe fills missing evidence | Compatible immutable report and digest-matching evidence evaluate offline under independently versioned catalogue | Permit one host query or ignore report version; CatalogueReportOnly / ReportVersionBound |
| PQ-T37; U04/18/19/24, FX-05/21 | Unsupported native/shared/nested/debugger/privileged-container/GPU/raw-device/snapshot/warm-clone/Mac requirement causes no VM/helper/native fallback or unadmitted device effect | Representative supported build/test completes via independently observed CPU-only cold Firecracker tuple | Remove unsupported-request gate only; UnsupportedClassDenied |
| PQ-T38; protocol §6/8, RH-T05/06 | Stale/unknown/unreachable revocation, expired record, old challenge/boot/frontier or revoked component causes no current matrix/host/admission claim; copied software key proves no physical identity | Fresh independently authenticated index, current local pin/boot and challenge bind own enrolled host; qualified hardware proof only if explicitly required and available | Skip freshness/frontier/challenge separately; CurrentHostRecord / RevocationRollbackDenied |
| PQ-T39; PR-21/22, CO-05/16/17/21 | Observer/retry/new identity cannot mutate start, lose retained debt, reset original clocks or release resources before intact effect reconciliation; blanket refusal cannot replace required authorized recovery | Bounded same-attempt observation and original-effect recovery actually complete with intact evidence, without duplicate start; other-principal control still works | Omit unknown-path effect/debt/recovery gate; ObserveDoesNotStart / IntactRecoveryWorks / DebtRetained |
| PQ-T40; CO-01/22, RH-T07/08 | Missing owner decision, exact accepted record, independent fact check, published bound, carrier, current final evidence or completion witness refuses first public placement and broadened pilot; the pinned stock cold-start entry point with main-filter-after-resume ordering must refuse before allocation/API (StockColdStartPendingDenied) | Complete independently accepted exact tuple passes final current checks and reserves one job once; actual production execution through the separately qualified entry point with pre-API checks and all-required-filters-before-guest ordering evidence is required, never a refusal-only stock run | Drop only pending activation prerequisite/final recheck; ActivationBeforePlacement |

### 4.3 Timed and ordering obligations are independent

The catalogue MUST preserve every CIP CO-01–CO-22 requirement and its named
CW/CM counterexample/control, plus runner-host duties. The crosswalk below binds
proposal tests to those obligations; it is not executed coverage or a waiver.
Use original protected triggers and clocks. Each positive includes normal success
and transient unknown/stall resolved within the same bound, or before the same
dependent event. A permanently stuck witness must fail the named completion
assertion while ordinary success, safety fences and unrelated checks still pass.

| Completion obligation | Original bound or ordered event | Proposal cases |
|---|---|---|
| CO-01 admission | All checks before allocation/start | PQ-T03/04/40 |
| CO-02 weaker acceptance/disclosure | Before reduced admission/reply; fresh act at start/resume/restart | PQ-T06/32/35 |
| CO-03 validation | Whole immutable validation before publication/code | PQ-T07/11/12 |
| CO-04 apply/receipt | Full boundary and durable verified receipt before first instruction | PQ-T07 |
| CO-05 physical start identity | Original effect resolved before start/replacement claim | PQ-T23/39 |
| CO-06 cancellation | Accepted cancellation + B-stop | PQ-T21 |
| CO-07 expiry/limit | Original runtime/resource trigger + B-stop, no extended ceiling | PQ-T16/18/21 |
| CO-08 revocation | Immediate start/release fence; original security detection + B-revoke (≤ 300 seconds baseline or exact current bounded owner exception) for actual disposition | PQ-T30 |
| CO-09 whole stop | Whole-account quiet-period zero and helpers absent by B-stop | PQ-T21 |
| CO-10 network | Route/session closed before next forbidden effect; also B-network | PQ-T13/15 |
| CO-11 destroy/reset | Confirmed absence plus custody/discard prerequisites + B-destroy, before reuse | PQ-T22/23 |
| CO-12 private cache | Actual private cache destruction by same B-destroy, before next execution | PQ-T09/22 |
| CO-13 custody | Full content outside deletion scope or exact discard before deletion | PQ-T24 |
| CO-14 intake | Complete bounded accepted upload + B-intake: whole publish or terminal refusal | PQ-T25 |
| CO-15 provenance/delivery | Complete bindings/content or explicit incomplete disposition before success/retrieval | PQ-T26 |
| CO-16 usable reserve | Actual recovery under saturation within original stop/revoke bound | PQ-T17/20/39 |
| CO-17 accounting | Charge prior principal/global debt before further allocation; throughout retention | PQ-T19/39 |
| CO-18 time/currentness | Current external authority before effect, original clocks preserved | PQ-T03/32/38 |
| CO-19 requalification | Complete exact independent requalification before next start | PQ-T04/30/38 |
| CO-20 covered retention | Published expiry + B-delete with custody/disposition preserved | PQ-T27 |
| CO-21 recovery | Original intact effect resolved before dependent disposition/resume/replacement | PQ-T23/39 |
| CO-22 activation | Complete qualified carriers/evidence/decisions before pilot or broader admission | PQ-T40 |
| RH enrollment | Externally approved current generation before first admitted effect | PQ-T03 |
| RH drain | Accepted drain + B-drain: actual stop, cleanup and custody/disposition | PQ-T29 |
| RH retirement | Accepted retirement + B-retire: actual disposal, revocation and retained disposition | PQ-T31 |

No new numeric deadline is invented here. Owners must publish finite B-stop,
B-revoke, B-network, B-destroy, B-intake, B-delete, B-drain and B-retire, their
clock/trigger/confirmation/quiet-period rules and recovery reserve before serving.
Event-order custody/reconciliation MUST NOT acquire an invented timeout that
permits deletion or release with missing evidence. Retry, reboot, observer,
new receipt or identity MUST NOT restart any clock. Fenced/refused/unknown is
failure safety, never proof that a timed completion obligation was discharged.

The already-published CIP-0026 security-revocation baseline is at most 300 seconds
from detection, including confirmation/quiet periods. A longer bound or later
trigger requires its explicit, independently authenticated bounded current owner
exception, naming
baseline and exact actual bound/trigger, reason/risk, declaration/profile/tuple and
execution scope, and validity. Admission policy MUST actually enforce it; original
and actual bounds, exception scope/validity and weaker status MUST be visible in
receipt/reply evidence. Start/resume/restart MUST recheck current authority time.
An exception MUST NOT extend a clock retroactively or waive independent completion
evidence or fences. PQ-T30/PQ-T32 MUST reject missing, expired, wrong-scope or
retroactive exceptions; a valid exact current exception is the positive control.
Omitting only its scope/currentness gate must fail RevokeExceptionCurrent. All
these are proposed cases, not executed sensitivity evidence.

PQ-T22 runs separate direct seeded/inventory/reset subcases for disk/saved state,
memory (including swap/dumps), devices, network, caches, logs, temporary state,
processes/handles and credentials/identities. Each class needs its own stuck-reset
narrowing and intended failure; one passing deletion cannot cover all nine.
The CPU-only profile's forbidden-device rejection does not qualify device reset;
any proposed assignment stays unavailable until exact reset/DMA evidence exists.
For never-created passthrough resources, evidence must establish the closed device
inventory, actual request refusal and absence of assignment; no seeded live raw
device fixture is authorized. Allowed virtual block/network device state remains
in the VM inventory and must be destroyed/reset. A proved inapplicable resource
subcase states that scope explicitly; unknown is never inapplicable or clean.

### 4.4 Coverage and exclusions

All 22 prioritized backend U-cases are mandatory through this proposal:

| Backend cases | Proposal coverage |
|---|---|
| U01–U05 | PQ-T10, PQ-T08, PQ-T09, PQ-T07/37, PQ-T11 respectively |
| U06–U10 | PQ-T13/15, PQ-T13, PQ-T16, PQ-T17, PQ-T19/20 respectively |
| U11–U15 | PQ-T19, PQ-T18, PQ-T21, PQ-T23, PQ-T22 respectively |
| U18/U19/U20/U21 | PQ-T37, PQ-T04/37, PQ-T18/28, PQ-T12/20/25 respectively |
| U23/U24/U25 | PQ-T05/20, PQ-T37, PQ-T26 respectively |

U16 concurrent-sharing qualification, U17 snapshot/warm-clone qualification and
U22 Mac guest/toolchain qualification remain out of the initial offered profile,
under the backend choice's explicit deferrals. Their refusal is still required
by PQ-T37; granting the capability requires a new class, catalogue and qualification.
A controlled live-neighbour boundary fixture for PQ-T08 does not authorize shared
production placement. PQ-T18 tests a host task cap with a bounded trusted host-side
fixture; guest process pressure does not establish host PID growth. Strict guest
process-count requirements remain unavailable without their own qualified mechanism.

The protocol's 14 named §1 cases remain mandatory; PQ-T33–PQ-T36/PQ-T38 add pool
bindings without replacing them. The [runner-host specification](runner-hosts.md), reconciled with the backend evidence,
retains RH-T01–RH-T30 and their original scope, with additional corrected
subcases; every facet remains required.
CIP-0025 at the pinned revision additionally binds these existing proposal cases:

- PQ-T07/PQ-T23: EIC-4-09, EQ-37 and TO-31 require code-free idempotent preparation, a durable original-effect handle/policy digest and same-effect recovery after lost/partial replies; retain PrepareCodeDenied, PrepareDuplicateDenied, PrepareReplayAllowed and their independent narrowing controls.
- PQ-T12/PQ-T33/PQ-T39: EIC-8-09/EQ-36 require bounded structured diagnostics and zero synthetic credential/header/private-body values in diagnostic and qualification/recovery evidence channels on accepted and refused paths; retain SensitiveDiagnosticDenied, BoundedDiagnosticAllowed and per-channel exclusion/bound controls.
- PQ-T19/PQ-T39: EIC-9-03/EQ-26 require frozen/parked memory and retained state to remain charged until observed qualified actual release; retain FrozenChargeDenied, actual-release positive and its narrowing control.
- PQ-T23/PQ-T30: EIC-4-04/EQ-05 require immutable restrictions to change only through a fresh admitted physical execution after confirmed absence, with linked effects and preserved original input/receipt history; retain ImmutableRestrictionRestartDenied, PolicyHistoryRewriteDenied and their paired positives/controls.
- PQ-T37/PQ-T40: EIC-11-01/EQ-38 require separate tuple support, per-guarantee capability, qualification and activation axes; retain TupleSupportDenied, TupleSupportAllowed and the support-label/scope narrowing controls. `supported`, `restricted` and `unsupported` are support labels, never new guarantee statuses.

EIC's revised station messaging placement, station-caller purpose dependency
(EQ-08/35) and station security-revocation ceiling remain station obligations and
rollout prerequisites where applicable; they grant no pool identity/signing route
and do not substitute a station deadline for CIP-0026's pool baseline and its
explicit bounded owner-exception contract.
All applicable EIC pool clauses remain required. Station-only key, login,
account-journal or credential-service implementation is not supplied by this pool
profile. These are profile exclusions, not waivers of secret absence or platform-authority denial.
A coverage map must identify every required source clause/test, report observation,
positive, killed narrowing control and any explicitly justified out-of-contract
row before acceptance. A catalogue numbering/table check proves none of these.

## 5. Pool matrix section and status interpretation

The existing verified-platforms format is a station matrix with a complete map
of 42 shared registered IDs. The 17 `pool.*` IDs are defined by the pool CIP,
not that registry. They MUST NOT be inserted into a closed station status map,
station declaration, local qualification record, effect receipt or generation
journal. A station matrix row alone cannot qualify pool placement.

Propose a separately labelled **pool profile section** in the same public index,
using its seven columns and trust/submission rules, with a versioned extension
schema owned by the research catalogue owner and accepted by the publisher.
It must have two separately revision-bound maps: all 42 shared IDs exactly once
and all 17 pool-local IDs exactly once. This is 59 explicit entries, not a new
shared registry. Until this extension is approved/implemented, no pool row can
be accepted through the existing station-only format and pool admission refuses.
No example or verified pool row is created by this document.

A row MUST bind the exact canonical class descriptor/hash; pool profile,
catalogue, independent kit release/report format and both vocabulary revisions;
readable per-ID statuses/scope/mechanism/bounds/proof; immutable accepted signed
records; sanitized evidence manifests; actual observation/expiry times; and
authenticated publication/revocation revisions. Subject-specific accepted records
remain linked without putting host identity in the class ID. Only one consistent
whole-map record may support the current row; incompatible hosts/boots/scopes
cannot donate their best cells to an imaginary qualified host.

Use exactly `present`, `weaker-with-compensation`, `absent`, `PENDING-VERIFY`.
For each ID, the versioned catalogue MUST specify the proof criteria for all
statuses, including justified unsupported scope for `absent`. Missing evidence
is `PENDING-VERIFY`. Undefined criteria cannot justify present or compensated
claims. A weaker entry must disclose missing property, actual compensation,
finite bounds, residual risk and evidence; the annotation **implemented,
deployment qualification pending** is not a fifth status and grants no authority.

The 11 shared IDs required by the pool CIP are `isolation.read`, `ipc.isolation`,
`execution.descriptors`, `network.egress`, `process.stop`, `limit.cpu`,
`limit.memory`, `limit.disk`, `limit.io`, `update.requalified` and
`qualification.revocation`. Each keeps its full registry meaning plus the pool's
additional scope; grouped rows require independent evidence per ID. The other
31 shared entries still require explicit justified statuses. Station-specific
unsupported capabilities remain absent where proved; unverified ones remain
pending. Their omission or renaming cannot make the full map appear qualified.

The following is a requirements/coverage map, not an accepted matrix row. All
local entries other than the deliberately unsupported side-channel-elimination
property remain `PENDING-VERIFY` at this documentary baseline:

| Pool-local ID | Proposed proving cases | Initial documentary disposition |
|---|---|---|
| `pool.isolation.write` | PQ-T07/09/12 | PENDING-VERIFY |
| `pool.isolation.residue` | PQ-T22/23 | PENDING-VERIFY |
| `pool.isolation.exclusive` | PQ-T05 | PENDING-VERIFY |
| `pool.isolation.side-channel` | PQ-T05/37; requirement refusal only | absent: no advanced side-channel-elimination mechanism is claimed |
| `pool.network.ingress` | PQ-T14 | PENDING-VERIFY |
| `pool.credential.absent` | PQ-T01 | PENDING-VERIFY |
| `pool.identity.separated` | PQ-T02/03/10 | PENDING-VERIFY |
| `pool.limit.elapsed-work` | PQ-T16/21/32 | PENDING-VERIFY |
| `pool.limit.storage` | PQ-T19/22/39 | PENDING-VERIFY |
| `pool.limit.output` | PQ-T12/20/25 | PENDING-VERIFY |
| `pool.time.authority` | PQ-T03/32/38 | PENDING-VERIFY |
| `pool.input.integrity` | PQ-T09/11 | PENDING-VERIFY |
| `pool.input.interpretation` | PQ-T11/12 | PENDING-VERIFY |
| `pool.result.provenance` | PQ-T25/26 | PENDING-VERIFY |
| `pool.result.inert` | PQ-T12/25 | PENDING-VERIFY |
| `pool.journal.durable` | PQ-T23/24/39 | PENDING-VERIFY |
| `pool.control.bounded` | PQ-T17/19/20/39 | PENDING-VERIFY |

The required declaration/profile selects the applicable guarantees; all 59 need
not be present for a constrained class to be indexed. A required absent or
pending property MUST refuse that job. Qualified compensated scope still needs
the EIC's independently authenticated operator act binding exact matrix revision,
tuple, declaration, quantitative scope and act validity interval at start, resume
and restart, with visible disclosure. A new receipt cannot extend that act.
Mandatory pool minimums, secret absence and freshness cannot be waived.

An export key is limited to its own enrolled host. Publisher endorsement is a
separate act, not authority inherited from that key. Independently verify trust,
closed encoding/domain/signature, subject/epoch/sequence/predecessor, all class
and evidence digests, report/catalogue compatibility, proof scope and full maps.
Verifiers MUST NOT execute submitted scripts. Evidence must be obtainable and
hash-matching; secret or redacted evidence cannot silently retain a stronger claim.
Public validity MUST be at most 30 days and no later than any required dependency
expiry; profiles may require less. New submission dates never refresh observations.

Class/test/status/trust/compensation changes require independent catalogue review
and publisher acceptance of the exact candidate. Routine unchanged attachments
need approved automation and independent checks; none is implemented by this
proposal. Conflicts require visible investigation/withdrawal, not selection of
a preferred result. Keep expired/revoked predecessors as marked history. A matrix
row indexes evidence; PA-11 still verifies this particular current host locally
and externally, including fresh challenge/boot/input checks from the protocol.

## 6. Renewal, invalidation and running jobs

A current claim requires the separately signed sequential revocation index,
authenticated freshness and protected anti-rollback frontier. Unknown, unavailable,
stale or rolled-back status MUST NOT support current publication or admission.
An offline verifier may state only bounded historical knowledge. Index refresh,
clock skew and withdrawal bounds must be approved before activation; no refresh
number is inferred from the 30-day maximum attestation lifetime.

The following transitions are mandatory in addition to protocol renewal:

| Trigger | New admission / record effect | Existing work / required recovery |
|---|---|---|
| Any PC field differs, including image/VMM/jailer/kernel/firmware update | Immediately invalidate old tuple for placement; create new exact class and reviewed kit/test run; never copy old results | Close ingress/start/resume; apply CO-07/08/19 as applicable, retain original clocks and reconcile existing effects |
| Required component, image, key, class, catalogue acceptance or host identity revoked | Close new starts/releases immediately and withdraw affected current claim through scoped authority | Actual stop or separately qualified confirmed non-revoked narrowing by original B-revoke; otherwise stop, fences/debt retained |
| Failed/unknown live check, drift, boot change, expired record or uncertain time/revocation | No current-host claim, matrix-based admission or cached-health bypass | Fence new effects; protect original job budgets, run bounded disposition required by policy; unknown is never completed absence |
| Suspected compromise or unexpected boundary effect | Externally durable fence, pool-identity revocation and independent upstream isolation | Apply RH incident disposition; trusted independently approved rebuild/replacement, fresh enrollment and qualification before re-entry |
| Drain/retirement request | No new placement; preserve exact generation and original accepted trigger | Actual stop, cleanup and custody/disposition by B-drain or B-retire; incomplete work cannot be relabelled completed |
| Same unchanged qualified inputs, ordinary boot renewal | Fresh native local qualification/current boot and applicable tests under same accepted pin, plus fresh public sequence when required | Protocol renewal does not repin drift, authorize automatic serving or reset live obligations |
| Evidence/publisher notification failure | Withhold current claim when required freshness cannot be established; retain pending withdrawal/delivery | Local/external response deadlines remain original; notification, resubmission or operator acceptance cannot reset them |

A qualified narrow-to alternative must already have a valid non-revoked boundary,
current acceptance and actual confirmation within the original revocation bound.
An unconfirmed request or fresh guest is insufficient. The initial profile offers
no automatic alternate backend. Broken state cannot open a recovery bypass.
Authorized bounded observe, reconcile and stop remain available only under their
intact state-integrity prerequisites, per EIC; blanket refusal is not successful
recovery. Reconcile original launch and manifests without duplicate effects.

Local qualification, public record validity, matrix publication, pool enrollment,
operator weaker acceptance and placement reservation have distinct lifetimes.
Renewing one MUST NOT renew the others. Protected original effect/debt records
survive reboots/restores/key rotation. A compromised key cannot restore its own
trust; rotation needs approved continuity or independent re-enrollment. Class
revocation and host-key revocation retain their separate scopes; an own-host
exporter cannot revoke other hosts or grant publisher authority.

## 7. Whole-pool activation refusals

The controller MUST refuse the first public job and every broadened profile until
all applicable gates below have persisted independently reviewed evidence. A
merged specification, owner recommendation, test definition, shell fixture pass,
signed self-report or green structural check cannot discharge these gates.

D-01–D-10 are the existing backend-choice decisions, not new approvals:

| Gate | Required decision/evidence | Refusal while unresolved |
|---|---|---|
| AG-01 / D-01 | Owner accepts exact hardware-backed Firecracker class or commissions a new comparison | No backend selected by default, runtime fallback or Sentry substitution |
| AG-02 / D-02 | Owner approves exclusive physical allocation including every outer sharing scope and residual leakage | No public allocation while sharing/topology policy is unknown |
| AG-03 / D-03 | Owner explicitly selects Linux-first scope and resolves any applicable service rights | No Mac/Xcode or Linux-on-Mac admission by analogy |
| AG-04 / D-04 | Owner approves known bare-metal CPU/KVM/firmware tuple and evidence of topology | No nested/composed class or unverifiable parent admission |
| AG-05 / D-05 | Owner approves finite root setup/stop/cleanup duties and non-root VMM policy | No silently privileged launch or ignored zero-root requirement |
| AG-06 / D-06 | Qualification owner supplies a disposable representative test machine, independent observability, test owner and reviewer | No use of an agent/service host or unrun class as qualification |
| AG-07 / D-07 | Network owner publishes exact destinations/protocols/DNS/redirect/failure policy and independent upstream enforcement | No general internet/platform routes or ping-only network qualification |
| AG-08 / D-08 | Owner approves actual successor-visible removal/reset standard, retention/custody/deletion and exclusions | No promised forensic erasure or reused state without exact evidence |
| AG-09 / D-09 | Budget owner publishes measured hard/soft scopes, caps, all original bounds, clocks, quiet periods and actually usable recovery reserve | No public pilot with guessed limits, incomplete lifecycle SLO or nominal reserve only |
| AG-10 / D-10 | Independent fact-check owner resolves backend-source claims, advisories/limits and reviewed exact-tuple activation authority | No host-independent truth label or activation from provisional evidence |

Additional gates cannot be satisfied merely by resolving owner choices:

| Gate | Required persisted evidence | Refusal when missing/unknown |
|---|---|---|
| AG-11 Contract integration | Accepted linked CIP/EIC/runner-host revisions; published typed module carriers, pool declaration/profile, receipt/currentness and gateway consumers; required EIC rollout prerequisites; reviewed source-to-binary ordering provenance with protected launch bindings or separately qualified equivalent pre-execution boundary | No draft-only interface or station-authority borrowing; stock cold boot resumes vCPUs before its main VMM filter and remains pending |
| AG-12 Catalogue/kit | Extension approved by the specification maintainers, all per-ID status criteria, compatible closed report/descriptor/envelope codecs with finite bounds and positive/negative vectors; reproducible reviewed kit | No catalogue-side probing, guessed format or undefined status proof |
| AG-13 Real qualification | Exact production service/identity/privileges/descriptors/mounts/CLI/environment; all 40 blocking cases, required source tests, nine-class subcases, timed controls and complete logs with actual exits; real production-execution positive, full thread inventory and ordering/identity evidence, and missing-filter/debug/disabled/allow-default/count-as-identity negatives | No truncated/skipped evidence, interactive-shell substitute, surviving required control or structurally inferred sensitivity; post-start observation corroborates only; stock pending refusal cannot pass the unavailable execution positive |
| AG-14 External trust | Independent provisioning/enrollment, own-host export registration, pool-only identity, approved custody, qualified hardware evidence if required, current revocation/frontier/time/withdrawal policy | No self-approved identity, copied key as physical proof or stale status |
| AG-15 Accepted class/host | Approved pool matrix extension and exact-class whole-map accepted record/evidence, current host challenge/local completion and independent qualification acceptance | No station row, another host's report, expired record, mixed cells or pending submission as admission |
| AG-16 Independent enforcement | Live independent upstream deny, scoped gateway/current-generation final checks, no platform secrets/authority, immutable launch policy and inter-service serialization | No capability from configuration intent, broker class exception or asynchronous stale grant |
| AG-17 Lifecycle/data | Durable original effect/ownership ledger, functional outside-scope full-content custody, actual bounded stop/reset/recovery/deletion, approved retention/admin policy | No digest-only custody, quarantine-only completion, unknown resource or lost retained obligation |
| AG-18 Final reservation | External final current check of tuple/record/identity/revocation/debt/fences/capacity and atomic exclusive reservation | No cached health, duplicate allocation, stale weaker act or debt reset on restart; stock main-filter-after-resume ordering denies allocation/InstanceStart |

If required new controls cannot be implemented within the approved architecture,
keep admission closed and return the exact ownership/platform decision. A test-only
stub, compensating fallback or a weaker declaration cannot qualify a mandatory
missing property. Wider workloads require a new reviewed profile/class and the
same activation gates, not an unrecorded change to the initial pilot.

## 8. Owner questions

These decisions supplement D-01–D-10; their resolutions must name authority,
exact scope/revision and evidence, rather than a blanket permission to operate.
Protocol-level decisions already fixed here are own-host export authority with
owner approval/separate registration, qualified-only hardware binding, closed
domain-separated Ed25519, maximum 30-day public validity, immediate drift
invalidation, signed sequential anti-rollback revocation, exact classes and
research ownership of catalogue versions. These are not reopened below.

1. **Q-PQ-01 — Qualification and enrollment authorities.** Which independent roles approve provisioning, exact production qualification, enrollment and final serving? Recommend separate recorded approvals and external final controller checks; unresolved ownership refuses serving (AG-06/10/14/18).
2. **Q-PQ-02 — Profile/catalogue publication.** Who accepts the pool extension schema/59-entry two-map section and which first immutable catalogue/kit-report versions are compatible? Recommend research-owned criteria plus publisher review, with station codecs unchanged; no accepted extension means no pool row (AG-12/15).
3. **Q-PQ-03 — Key custody and physical claim.** Approve protected software custody labelled self-report, or require a separately qualified hardware mechanism? Decide export versus pool-key separation/rotation/recovery; no hardware claim from copied software state and no current claim without registration (AG-14).
4. **Q-PQ-04 — Freshness distribution.** What finite index refresh, maximum skew, challenge freshness and withdrawal deadlines apply, and who protects sequence high-water state across recovery? Recommend event-driven revocation plus bounded refresh and fail-closed unknown; no invented refresh default (AG-14/18).
5. **Q-PQ-05 — Production test allocation.** Which disposable representative machine, bounded fixture envelope, independent upstream observer and test/review owners satisfy D-06? Recommend isolated Linux hardware and explicit root/destructive fixture authorization; no testing on service hosts (AG-06/13).
6. **Q-PQ-06 — Budgets and completion.** What measured caps, quiet periods, B-stop/revoke/network/destroy/intake/delete/drain/retire and usable reserve discharge D-09? Recommend one small fixed pilot envelope with original clocks; refuse every undefined required bound (AG-09/13).
7. **Q-PQ-07 — Evidence and privacy.** What sanitized proof remains publicly retrievable through record expiry plus audit window, who approves disclosure, and how is private complete evidence retained? Recommend content-addressed minimized exports; withhold unsupported/redacted claims rather than leaking raw logs (AG-12/15/17).
8. **Q-PQ-08 — Cleanup and incident recovery.** Approve exact nine-class reset methods/standard and who independently accepts rebuild versus replacement after possible firmware/outer-host compromise? Recommend successor-visible removal with explicit forensic limits and replacement if trusted recovery is unestablished (AG-08/17).
9. **Q-PQ-09 — Gateway/network bindings.** Which versioned enrollment/gateway/currentness protocols and independent upstream policy implement D-07 without platform authority? Recommend typed scoped pool actions and default deny; unresolved wire/lock/final-check ownership refuses activation (AG-07/11/16).
10. **Q-PQ-10 — Result and retention services.** Who owns full-content custody, whole-result intake, covered-copy deletion, per-act discard and separate minimal audit expiry? Recommend host-reported evidence only and outside-destruction custody before deletion; unavailable services refuse jobs (AG-08/17).
11. **Q-PQ-11 — Publication safety path.** Which publisher may accept exact class/status changes and perform bounded revocation/expiry withdrawal when routine submission is unavailable? Recommend independent review for new claims and scoped preauthorized safety withdrawal; no current claim on an unknown publisher frontier (AG-14/15).
12. **Q-PQ-12 — Backend fact check and rollout.** Who signs the independent source/advisory fact-check disposition, resolves older mapping pins and accepts all companion/carrier rollout prerequisites before the exact initial class opens? Recommend a separately recorded review and activation act; D-10 remains open until it exists (AG-10/11/13).

[protocol]: https://github.com/relux-works/swarma-user-manager/blob/5a193574575bd0a4681a0d5fe70dc15792bc29f8/spec/host-qualification-protocol.md
[verified]: https://github.com/relux-works/swarma-user-manager/blob/5a193574575bd0a4681a0d5fe70dc15792bc29f8/platforms/verified.md
