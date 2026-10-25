# Public execution pool runner hosts

Status: Draft (2026-10-25).

No host is enrolled, qualified or enabled by this document. Platform ownership does not make a host honest or its reports truthful.

## Scope and dependencies

This specification defines dedicated host operation and disposable job runners.
It builds on the [accepted threat model](../research/threat-model.md) and
[backend evidence](../research/isolation-backends.md); threat and control IDs
retain their meanings there. Requirements are obligations, not measured properties
or authorization to install a backend on an existing machine.

The companion Execution Isolation Contract supplies profiles, capability vectors,
execution bindings, boundary receipts and qualification tuples. The foreign-code
execution contract supplies job declarations, refusals, custody and the untrusted
class. Their publication and reviewed linkage remain activation dependencies.
The companion host admission and qualification specification owns the enrollment
protocol, evidence formats and final placement acceptance. No new wire schema or
existing station/workplace authority is introduced here.

### Provisional backend binding

The [backend choice](../research/backend-choice.md) defines the initial qualification
target: one fresh cold Firecracker microVM per job under the jailer, including its
VMM, helpers and storage objects. Initially each physical host MUST have at most
one live or unknown job allocation, with no concurrent foreign job or co-tenant
anywhere in that physical scope. Hosts MUST be dedicated bare-metal
Linux/KVM machines; nested/composed placement is unavailable for this initial class.
The launcher MUST NOT automatically fall back to native execution, another VMM,
a shared sandbox or a warm/snapshot clone when this tuple is unavailable.

Primary-source reconciliation supports this first qualification target. The choice
remains provisional pending independent acceptance of the corrected requirements,
the backend choice's owner decisions and exact production-tuple qualification. It
activates no host. Exact versions, digests, device policy, privileges, limits and
response/reset evidence remain required published parameters before serving.
An unresolved parameter is unavailable support; a product name implies no guarantee.

| Parameter | Required published binding |
|---|---|
| Unit and substrate | One cold Firecracker microVM under the jailer per job on dedicated bare-metal Linux/KVM; one live or unknown job per physical host initially, no automatic fallback; exact VMM/jailer/guest/host versions, architecture, firmware and KVM capabilities per [backend choice](../research/backend-choice.md) |
| Launch authority | Operator-owned image/configuration digests, service identity, setup privileges and typed helper operations |
| Boundary | Positive read roots, write scope, descriptor set, service purposes, mounts, device inventory and initial exclusive physical-host allocation per [backend choice](../research/backend-choice.md) |
| Accounting | Whole-unit CPU/memory/process/storage/I/O/network/output ceilings, actual applied cgroup/controllers/effective limits and complete charged scope before first instruction; scheduling classes, measured overshoot envelope/headroom and usable recovery reserve |
| Mitigations | Exact CPU mitigation policy, firmware/microcode, SMT and memory-deduplication (KSM) state with required drift checks; unknown/changed mandatory state refuses |
| Stop/reset | Whole-account inventory, quiet period, destruction/reset/erase procedure and evidence for each resource class |
| Connectivity | Pool gateway, independent upstream isolation, resolver/egress policy and host management scope |
| Evidence | Exact current qualification, external approval/revocation authorities and protected lifecycle journals |

## Roles and authority separation

| Role | Enforced responsibility |
|---|---|
| Pool operator | Dedicated provisioning, audited administration, rebuild and retirement |
| Enrollment authority | Independent approval and external issuance/revocation of pool-only host identity |
| Qualification authority | Review exact production-service evidence; a host cannot self-qualify |
| External admission controller | Bind current host generation, reserve capacity, serialize fences and prevent duplicate effects |
| Host supervisor/launcher | Apply operator-owned boundaries, record local effects, stop and clean the complete unit |
| Pool gateway | Independently verify current pool identity and exact action/object scope |
| Network policy owner | Independent upstream isolation plus per-job resolver/egress enforcement |
| Result/retention services | Bounded inert intake, complete-content custody, authorized retrieval and deletion |

**RH-01.** A serving host MUST be a separate platform-owned machine dedicated to
the pool. It MUST contain no platform private signing key, subscription binding,
broker lease, infrastructure credential, platform-agent messaging identity or
unrelated operator data, including in images, backups and crash dumps.
The untrusted class MUST receive no broker binding, issue or renewal.
Qualification: RH-T01.

**RH-02.** Hosts and runners MUST have no platform repository write authority or
direct platform-control route. Their only platform-facing interface MUST be the
declared mediated pool gateway. That gateway MUST NOT forward arbitrary URLs,
sockets, commands or platform actions from a pool request.
Qualification: RH-T02, RH-T22.

**RH-03.** A host MUST hold only its own pool enrollment/reporting identity, public
verification material, approved configuration and scoped job material for current
or explicitly retained obligations. Its key MUST be inaccessible to runners and
job authors and authorize only that host generation's pool operations, never
station, broker, signer, registrar or platform messaging actions.
Qualification: RH-T01, RH-T03.

**RH-04.** The enrollment authority MUST issue identity only after fresh host-key
proof, independent provisioning approval and exact host/boot/generation binding
are recorded externally. Issuance MUST NOT copy a platform secret to the host or
let a host-signed enrollment assertion approve itself.
Qualification: RH-T04.

**RH-05.** Gateway and admission consumers MUST independently verify scope,
generation, validity and external revocation at their final action decision,
including retries and asynchronous completion. Revocation MUST invalidate old
sessions and prevent new authorized effects without awaiting host logout.
Qualification: RH-T05.

**RH-06.** A compromised pool identity MUST NOT gain platform authority, another
host's scope or another requester's objects. Gateway quotas and independent
upstream containment MUST bound its remaining traffic. Reports MUST retain the
host-claim evidence level, not independent proof of computation, absence, cleanup
or attestation.
Qualification: RH-T02, RH-T05, RH-T26.

## Host lifecycle

The external host record is separate from the local journal. Enrollment generation,
boot incarnation, qualification tuple and job attempt are distinct bindings.
The table describes transitions and recording owners, not an executable API.

| Transition | Required evidence and owner |
|---|---|
| Absent → provisioned | Operator: approved image/provenance, full machine inventory and isolated network attachment |
| Provisioned → enrolled | Enrollment authority: fresh key proof, independent approval, new generation and scoped validity |
| Enrolled → qualification pending | Controller: exact tuple; serving authority withheld and no jobs dispatched |
| Pending → serving | Qualification authority publishes acceptance; controller independently checks the same current tuple |
| Serving → draining | Controller closes placements and records original trigger, deadline and live/pending inventory |
| Draining → drained | Controller: actual bounded stop, full cleanup and custody/disposition for every obligation |
| Drained → serving | Controller: current qualification, resolved effects, cleanup and operator authorization |
| Any live state → fenced | External authority: start/identity denial and upstream isolation; attempts remain accounted |
| Fenced → reprovisioned | Operator: approved wipe/rebuild or replacement; fresh enrollment and qualification |
| Drained/fenced → retired | External authority: identity/access revocation, disposal and retained-object disposition |

**RH-07.** Provisioning MUST use an approved digest-pinned host image and complete
operator-owned backend/configuration inventory. Boot, firmware, outer-host and
material policy changes MUST invalidate affected qualification before placement.
Revoked images/tuples MUST NOT return through rollback or restored state.
Provisioning and each placement MUST bind CPU mitigation policy, firmware/
microcode, SMT and memory-deduplication state to the admitted tuple. Changed or
unknown mandatory state MUST close admission. Physical exclusivity MUST NOT be
reported as elimination of hardware side channels.
Qualification: RH-T06, RH-T07.

**RH-08.** Enrollment MUST NOT enable serving until independently accepted
qualification covers exact production entry, identities, privileges, backend,
limits, reset and network policy. Missing evidence or an unresolved required
parameter MUST refuse placement without native or privileged fallback.
Qualification: RH-T07.

**RH-09.** The controller MUST serialize serving/drain/revocation with final
placement and durably reserve the exact host generation and job scope. Cached
health, a new request ID or restored local state MUST NOT reopen an external
fence, discard debt or duplicate an unresolved allocation.
Qualification: RH-T08.

**RH-10.** Accepted drain MUST immediately close placements and actually finish
affected stop, cleanup and custody/disposition within its original published
bound. Work unable to finish in time MUST be cancelled without extending its
execution budget. Missing completion MUST leave the host fenced/out of service;
a drain label or empty queue is insufficient.
Qualification: RH-T09.

**RH-11.** Suspected compromise MUST cause an externally durable fence, identity
revocation and independent network isolation, with immediate new-start denial and
actual affected-job stop or separately qualified confirmed non-revoked narrowing
within the original revocation bound. A fresh guest or host clean report MUST NOT
clear the fence. Authorized recovery MUST retain intact-state prerequisites;
uncertainty MUST NOT open another launch, release or control path.
Under AD-04 a compromised host's own stop, health or cleanup report MUST NOT
establish completion. External admission and network fences MUST remain effective.
Any promise of bounded physical stop against that adversary MUST name and qualify
an independently controlled mechanism and observer outside the compromised host
TCB. Without that independent evidence completion MUST remain unknown and reuse
MUST stay prohibited; fencing alone MUST NOT pass a required completion deadline.
Qualification: RH-T10, RH-T17.

**RH-12.** Post-compromise re-entry MUST require independently approved trusted
rebuild, fresh enrollment and qualification, or retirement. Possible firmware,
management-controller or outer-host persistence MUST be treated as beyond guest
cleanup; an unestablished recovery MUST keep the machine fenced or replaced.
Ownership and a signed wipe report are not cryptographic proof of erasure.
Re-enrollment MUST require separately authorized recovery evidence; it MUST NOT
retroactively satisfy a missed stop, cleanup or revocation deadline.
Qualification: RH-T10, RH-T11.

**RH-13.** Retirement MUST revoke identity/placement authority, close network and
administrative access, and complete published sanitization/destruction and
retained-object disposition before recording retirement complete. Unknown custody
or reset MUST preserve the external record, fence and resource debt.
Qualification: RH-T11, RH-T27.

## Runner lifecycle

One runner is one physical job attempt, including startup, package hooks,
subprocesses and indirect workers. A new job gets one new cold Firecracker microVM
under the jailer, per the [backend choice](../research/backend-choice.md), never a
persistent agent station
or warm/snapshot clone. Controller restart reconciles the original effect.

**RH-14.** Before allocation the controller MUST durably bind requester,
declaration, input/image digests, attempt, host/boot generation, tuple/profile,
policies, execution-account generation, requested guarantees and effect IDs.
At most one live or unknown execution MUST occupy an admitted scope; replacement
MUST wait for confirmed absence and fenced predecessor ingress.
Qualification: RH-T08, RH-T12.

**RH-15.** The launcher MUST create one fresh cold Firecracker microVM under the
jailer from approved pinned images, per the [backend choice](../research/backend-choice.md),
with private writable disks, memory, caches, temporary state, job network identity and
job-scoped material. It MUST NOT clone predecessor writable snapshots, random/
application identities, live connections or credentials into a job. Shared base
content is limited to authorized digest-verified read-only objects.
Cold boot MUST NOT count as sanitization of a replicated root image. Image
acceptance MUST cover embedded machine/application identities, random seeds,
credentials and prior workload state as well as publisher/build provenance and
revocation. The exact guest stack MUST qualify early-boot entropy readiness and
per-execution identity. A small duplicate-free cohort is only a regression
observation and MUST NOT be labelled proof of randomness.
Qualification: RH-T13, RH-T18, RH-T20.

**RH-16.** Each unavoidable outside interpreter of job bytes MUST be inventoried,
admitted and constrained under TC-25, including image/layer assembly, staging,
transfer framing, telemetry, recovery and intake. It MUST enforce closed schemas,
safe paths/types and streaming byte/count/expansion/work limits. Refusal MUST cause
no outside-root write, hook/discovery, partial publication or payload start.
Prefer opaque transfer and interpretation inside the applied boundary.
Qualification: RH-T16.

**RH-17.** The launcher MUST apply full read/write, descriptor, device, service,
network and resource restrictions and publish a protected receipt bound to admission
before controlled code. Separate start evidence MUST identify physical launch.
A boundary receipt MUST NOT imply start, stop or absence; uncertain publication/
start MUST reconcile the same effect rather than launch another unit.
Before `InstanceStart` or any job-controlled instruction, the launcher MUST
verify the actual applied host cgroup, required controllers, effective limits
and complete charged-process scope. Successful jailer/VMM start MUST NOT substitute
for these observations: the documented cgroup-v2 path with no `--cgroup` arguments
can continue without moving when its requested parent is absent. Missing or
mismatched applied state MUST refuse admission.
Only the approved production build target, exact binary and restrictive filter
identities MUST be admitted. The admitted profile MUST explicitly inventory all
execution and guest-facing threads, including every vCPU, the main VMM thread,
the API thread when enabled, and any helper/worker thread in that profile. For each,
record its creation/lifetime, guest-facing role, required filter identity, installation
or inheritance path, failure handling and ordering relative to the first guest
instruction. An omitted thread or unknown required installation order MUST close
admission. A thread created later MUST NOT introduce an unfiltered execution or
guest-facing path; the complete lifecycle MUST satisfy the admitted ordering policy.
The launcher MUST NOT permit any guest instruction before all filters required
by the profile are installed. Absence of a disabling flag or an `apply_filter`
success MUST NOT suffice.
Debug builds, `--no-seccomp`, empty required filters, allow-default
filters and unsupported-target default-policy fallbacks MUST refuse admission,
with no guest release or partial job effect. Protected setup paths and jailer inputs
MUST be operator-owned. The fresh job identity and per-job VMM UID/GID MUST be
explicit, with privileged supervisor setup, observation, stop and cleanup authority
recorded separately.

These are two distinct launch phases. Before `InstanceStart`, the launcher MUST
verify the actual host cgroup/controllers/effective limits and charged scope,
the admitted production build, the complete thread inventory and exact restrictive
filter identities. This phase MUST NOT demand live observations of not-yet-created
threads. Qualification MAY establish actual filter-installation ordering by reviewed
source-to-binary provenance: pin reviewed sources, build recipe/toolchain/target,
features and dependencies, produced executable and filter bytes, all thread creation
and filter paths, and fail-closed installation semantics. Protected launch evidence
MUST bind the running bytes, selected filters, attempt and policy to that reviewed
sequence, under the declared trusted-host/kernel assumptions. The sequence MUST
install all required filters before any guest instruction, aborting or withholding
release on missing, mismatched, failed or unknown installation. A qualified protected
hold/verify/release interface or separately qualified equivalent pre-execution
boundary MAY supply that guarantee; a live external observation is not mandatory
when the complete ordering is established by the reviewed provenance. A later
thread-status check only corroborates it and MUST NOT replace ordering or identity.
`Seccomp` and `Seccomp_filters` in `/proc` report mode and count — unlinked snapshot reference (no public commit pin): `https://man7.org/linux/man-pages/man5/proc_pid_status.5.html`,
not filter identity; equal counts MUST NOT establish equivalent policy. These
trusted-host evidence assumptions do not turn AD-04 local reports into independent
proof of a compromised host's state.

**Pinned cold-start limitation.** Firecracker v1.17.0 source at
`95f868c8e345b1cc8faccd1a3c910b4989dc3f58` installs each
[vCPU's own filter before its first `KVM_RUN`](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/vstate/vcpu.rs#L217);
installation failure panics and the [production panic policy aborts the process](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/Cargo.toml#L37).
But [`build_and_boot_microvm` resumes vCPUs](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/builder.rs#L373)
before [the API caller installs the main VMM thread's filter](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/firecracker/src/api_server_adapter.rs#L222).
The [JSON/no-API path has the same ordering](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/firecracker/src/main.rs#L642).
A guest may therefore execute while the main VMM thread is still unfiltered;
this fails the strict gate even if all later observations show filters installed.
There is also no stock external all-thread verify-then-release step. This does
not claim that vCPUs run without their own required approved filters or that
seccomp is unverifiable. [Debug/unsupported-target builds select a fallback policy](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/firecracker/build.rs#L26)
whose [default action is allow](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/resources/seccomp/unimplemented.json#L1);
[`--no-seccomp` selects empty filters](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/firecracker/src/seccomp.rs#L37),
and [applying an empty filter returns success without installation](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/seccomp.rs#L94).

The strict pre-guest guarantee remains **PENDING-VERIFY** for stock cold boot,
and external admission MUST refuse before allocation or `InstanceStart`.
A launcher/patch installing the main filter before resume and then verifying, or
[paused snapshot restore with `resume_vm=false`](https://github.com/firecracker-microvm/firecracker/blob/95f868c8e345b1cc8faccd1a3c910b4989dc3f58/src/vmm/src/rpc_interface.rs#L643),
requires its own exact-profile qualification; snapshots remain outside this cold-only
class and need separate image/residual-state/entropy acceptance. A post-start check
or the narrower “before handling guest-controlled input” contract requires an
explicit owner contract decision, never silent substitution. The owner options in
[backend choice](../research/backend-choice.md#linux-primary-configuration-and-alternatives)
are unresolved; this specification implements or approves none. A refusal-only run
MUST NOT pass the missing production-execution positive of RH-T14.
Qualification: RH-T07, RH-T12, RH-T14, RH-T15, RH-T17.

**RH-18.** Read isolation MUST deny actual unrelated host and live-neighbour reads
through global/temp paths, links, aliases, mounts and inherited handles. No
unauthorized content MUST reach execution-visible responses, logs or artifacts.
Write denial or an unchanged canary MUST NOT substitute for confidentiality.
Qualification: RH-T13.

**RH-19.** Host sockets, privileged backend APIs and undeclared descriptors MUST
remain inaccessible to jobs. Every permitted service binding MUST deny forbidden
purposes on the same authenticated peer/endpoint with no unauthorized read,
state change, spawned work or onward request. Guest/host UID equality MUST NOT
authenticate a platform peer.
Qualification: RH-T15.

**RH-20.** Observe/status MUST report bounded current evidence for the same attempt
without starting, resuming or changing it. The journal MUST distinguish running,
exited, stopping, absent and unknown; preserve debt/reservations while unknown;
and support intact authorized recovery of the original effect without duplicate
launch, stale signal or fabricated completion.
Qualification: RH-T12, RH-T17.

**RH-21.** Cancellation or a runtime/resource-limit trigger MUST fence new ingress,
start and resume, resolve in-flight launch and stop the complete job within its
original bound. Completion MUST include whole execution-account zero over the
published quiet period and absence of its Firecracker VMM, jailer, helpers and
attributable work. Leader exit/backend status is insufficient. A stale stop MUST NOT signal
another execution or successor generation.
These local absence observations rely on a trusted host kernel/observer. Under
AD-04, RH-11's independent mechanism and observer MUST supply any claimed physical
stop completion; a local zero counter or signed report MUST NOT suffice.
Qualification: RH-T17, RH-T19.

**RH-22.** Collection MUST run only at the qualified fenced/quiescent point.
Before source deletion custody MUST contain every bounded output blob outside
the deletion scope or an authenticated explicit discard. The collector MUST bind
complete actual runtime-dependency digests and request/attempt/image/policy to
results; a digest-only manifest is not complete-content custody.
Qualification: RH-T24, RH-T25.

**RH-23.** The supervisor MUST actually destroy/reset all owned resources and
confirm cleanup by the original destroy bound after absence and custody/disposition.
Unknown stop, reset or collection MUST retain allocation, fences and debt and
remove the host from service. It MUST NOT become an assumed-clean reusable slot.
Qualification: RH-T20, RH-T21, RH-T24.

**RH-24.** Result intake MUST authenticate exact scoped upload, quarantine and
fully validate bounded objects before whole publication. Complete accepted intake
MUST reach whole publication or intended terminal refusal by its published bound,
without partial import. Consumers MUST treat artifacts, telemetry and report text
as inert untrusted data, never commands or platform instructions.
Qualification: RH-T16, RH-T25, RH-T26.

## Completion, clocks and evidence

**RH-25.** Before serving, the lifecycle owner MUST publish each trigger/bound,
clock authority, confirmation and quiet period below and bind them to the admitted
generation. Retry, reboot, observers and identity changes MUST NOT restart clocks
or extend runtime ceilings. Fenced, unknown, refused or reserved state MUST NOT
satisfy an outstanding completion obligation.
For security revocation, [CIP-0026](https://github.com/relux-works/curator-spec/blob/88b0d8615a5df8728e0e9540a9edd708c0ca5660/cips/CIP-0026-foreign-code-execution.md)
sets B-revoke to at most 300 seconds from detection, including confirmation and
quiet periods. A longer bound or later trigger requires its explicit, bounded and current
independently authenticated owner exception, bound to the exact scope and visible
in admission evidence. Publication MUST NOT be treated as qualification or activation.
Qualification: RH-T04, RH-T07, RH-T09, RH-T10, RH-T11, RH-T12, RH-T14,
RH-T17, RH-T19, RH-T20, RH-T21, RH-T22, RH-T23, RH-T24, RH-T25, RH-T27.

| Obligation | Original trigger and boundary | Actual completion; case |
|---|---|---|
| Enrollment | Before first admitted host effect | Approved scoped identity/current generation recorded externally; RH-T04 |
| Qualification/start | Before placement / first controlled instruction | Exact accepted evidence, applied boundary and durable verified receipt before code; RH-T07, RH-T14 |
| Drain | Accepted drain + B-drain | Affected executions absent, cleanup and custody/disposition complete; RH-T09 |
| Revocation | Security detection + B-revoke (CIP-0026 baseline ≤ 300 seconds, or exact current bounded owner exception); immediate fences at detection | Actual stop or separately qualified confirmed non-revoked narrowing; RH-T10 |
| Stop | Accepted cancellation or runtime/resource-limit trigger + B-stop | In-flight start resolved, whole-account quiet-period zero, helpers/work absent; RH-T17, RH-T19 |
| Network isolation | Before next forbidden effect and within B-network from policy failure/revocation | Actual route/session closure, no forbidden traffic; RH-T22, RH-T23 |
| Custody | Before source deletion | Complete retrievable content or exact authorized discard; RH-T24 |
| Destroy/reset | Confirmed absence and complete custody/disposition + B-destroy | Every manifested resource destroyed/reset with confirmation before reuse; RH-T20, RH-T21 |
| Intake | Complete bounded upload accepted for validation + B-intake | Whole publication or intended terminal refusal; RH-T25 |
| Retention | Published retention expiry + B-delete, preserving custody/disposition prerequisites | Covered data/log/backup copies actually deleted with evidence; RH-T27 |
| Retirement | Accepted retirement + B-retire | Revoked access/identity, approved disposal and retained-obligation disposition; RH-T11 |
| Reconciliation | Before dependent completion/reuse/release claim | Intact original effect resolved; no invented clock for event-only custody; RH-T12, RH-T24 |

**RH-26.** Every transition/effect MUST retain actor, operation identity, original
trigger/bound, host/boot/job generation, before/after state, qualification/policy,
resource inventory and evidence references. The controller MUST retain external
fences and debt across host restore. Host evidence MUST remain a local claim under
the qualified host TCB, not independent proof of host honesty.
Qualification: RH-T08, RH-T12, RH-T26.

## Closed cleanup and reuse rules

**RH-27.** The supervisor MUST journal complete ownership before resource creation
and reconcile each allocation/disposal checkpoint. Every object MUST belong to a
class below with exact qualified destruction and evidence procedures. Unknown types,
untracked objects or unqualified reset MUST refuse allocation or fence the entire
host if discovered later.
Qualification: RH-T20, RH-T21.

The backend binding supplies exact method/version, erase standard and verifier.
Unlink, key deletion, process exit or a successful API reply alone is not an erasure
argument. Disk, swap, saved state and backups are included, not just mounted roots.

| Resource class | Required destruction/reset | Required observed evidence | Missing/failed evidence |
|---|---|---|---|
| Disk/saved state | Destroy writable layers, volumes, backing copies, snapshots and saved-machine state; qualified sanitization or encrypted disposal covers every key/copy | No predecessor attachment; seeded disk/snapshot content unavailable to successor; erase record | Host fenced; object obligations/debt retained |
| Memory | Destroy unit/address spaces and reset exposed pages; dispose guest/host swap, hibernation and dumps | No live mapping/owner; qualified reset plus successor cannot obtain seeded prior memory | Host fenced; no claimed-clean allocation |
| Devices | Default absent; admitted assignment stops DMA and completes exact device/driver reset/sanitize | Assignment detached, reset verified, seeded state inaccessible and DMA isolation current | Host/device fenced; unqualified device denied |
| Network | Tear down job namespaces/interfaces/routes, NAT/proxy/DNS/conntrack state and identity bindings; revoke grants | Old endpoint/session cannot send, receive or authenticate; no predecessor mapping | Host out of service; independent upstream deny retained |
| Caches | Destroy private writable build/package/loader caches and metadata; only authorized immutable digest objects retained | Private objects absent; shared base read-only, digest-correct and authorized; successor reads own state only | Host fenced; no cache-reuse compensation |
| Logs | Transfer complete authorized retained output to separate bounded custody; delete local payload spools/buffers/dumps | Complete custody/local removal; no predecessor log exposed; separate retained-copy deletion evidence | Host out of service for unknown local residue; retained copies keep debt/expiry |
| Temporary state | Remove private staging, mounts, files, sockets and export scratch without following hostile links | Owned tree/mount inventory cleared, no outside-root deletion, prior markers unreadable | Host fenced; staging never promoted by retry |
| Processes/handles | Stop account work, helpers and indirect workers; close owned descriptors/control handles and invalidate stale actions | Whole-account quiet-period zero; helper/handle inventory absent; no stale successor signal | Host fenced; absence remains unknown |
| Credentials/identities | Revoke/destroy job tokens/keys and network/application IDs; fresh random state for successor | Old grant/session rejects at final consumer; no predecessor key/ID/connection state exposed | Host out of service; independent gateway revocation retained |

**RH-28.** A new job MUST NOT reuse predecessor writable disk, memory, cache,
temp, network identity, credential or saved state. An immutable base MAY be reused
only by exact digest, read-only and within current access scope. The loader MUST
verify these properties on every materialization.
Qualification: RH-T13, RH-T18, RH-T20.

**RH-29.** Host reuse MUST require confirmed absence, completed manifest cleanup,
current qualification and external approval of a fresh generation-bound runner.
Any cleanup error or missing observation MUST remove the host from service until
intact independent reconciliation establishes every prerequisite. Local success
or available capacity MUST NOT clear quarantine by itself.
After suspected host compromise, local cleanup/stop success MUST NOT authorize
reuse. The external authority MUST retain fences and unknown completion until
RH-11/RH-12's independent stop and separately authorized recovery prerequisites
are established; later recovery MUST NOT erase a missed completion deadline.
Qualification: RH-T20, RH-T21.

**RH-30.** Payload logs/artifacts and operator access MUST have disclosed retention,
authorized retrieval and covered-copy deletion evidence. Expiry MUST cause actual
covered log/artifact/backup deletion within B-delete after custody/disposition,
not merely access denial. Minimal lifecycle audit metadata MUST have separate
schema/access/expiry and MUST NOT retain payload content as a cleanup exception.
Qualification: RH-T24, RH-T27, RH-T30.

## Network enforcement

**RH-31.** Per-job egress MUST default to none and traverse qualified pool
enforcement. A destination declaration is only a request. The policy service MUST
intersect it with admitted policy, protocol/destination/traffic budgets and current
execution identity. Direct routes, proxy failure and host-origin escape traffic
MUST NOT bypass independent upstream containment.
Qualification: RH-T22, RH-T23.

**RH-32.** Resolver/proxy enforcement MUST recheck every DNS answer, resolved
address, connection, redirect and protocol across address families. Metadata and
link-local endpoints MUST remain blocked. Private, loopback, peer and platform
destinations MUST stay denied except an exact published minimal pool-service
purpose through the gateway under RH-02; no direct control route is created.
General Internet permission MUST NOT create an exception.
Qualification: RH-T22, RH-T23.

**RH-33.** Independent upstream filters MUST deny unsolicited inbound traffic;
only authenticated declared gateway management/transfer may reach a host. Allowed
dependency replies remain scoped return traffic, not inbound job listeners.
Jobs/authors MUST have no host shell or administrative login path, including
backend consoles, debug APIs and forwarded management endpoints.
Qualification: RH-T22, RH-T28.

**RH-34.** Policy changes, expiry, stop and revocation MUST close affected routes/
sessions before further forbidden effects and complete independent isolation within
B-network. Enforcement MUST derive identity from protected placement, not caller
headers/labels, and preserve old-generation denial after address reassignment.
Qualification: RH-T05, RH-T20, RH-T23.

## Privilege and operations

The provisional [backend choice](../research/backend-choice.md) requires bounded
root jailer/supervisor setup, stop and cleanup duties and a per-job non-root VMM
UID; it is not a blanket rootless claim. Exact privileges and delegation remain
subject to owner approval and production-entry qualification, not permission from
a job declaration. The operator-approved map separates setup from routine work:

| Operation group | Permitted privilege owner |
|---|---|
| Host image/boot, service installation and machine-wide isolation policy | Dedicated provisioning operator or its closed host service; no job-selected package hooks |
| Namespace/interface/device setup and delegation | Pre-provisioned operator resources where possible; exact typed setup helper if privileged access is required |
| Routine launch/observe/stop/collection | Qualified restricted service account using delegated handles; any unavoidable privilege is an explicit helper capability |
| Mount/device disposal and host retirement | Authorized bounded cleanup/provisioning service; never paths or shell commands supplied by jobs |

**RH-35.** Launch/observe/stop/collection MUST use least-privileged service identities
and pre-provisioned delegation wherever qualified. Required host root/setup MUST
be limited to operator provisioning or a qualified host service. Undocumented
backend privilege behavior MUST remain unavailable, never labelled rootless or
waived for compatibility.
The privilege record MUST distinguish the per-job VMM UID/GID from the
supervisor's setup, observation, stop and cleanup powers, and identify path and
descriptor owners. No upstream minimal capability set is established here.
Qualification: RH-T28, RH-T29.

**RH-36.** A privileged host service MUST expose only a closed typed, versioned,
action-specific interface bound to current host/job generation, effect ID and
operator-owned handles. It MUST reject job-selected host paths, mounts, raw devices,
runtime flags/templates, shell fragments and arbitrary executors before effects.
A shell inside a job unit grants no host shell or root authority.
Qualification: RH-T16, RH-T28, RH-T29.

**RH-37.** Pool administration MUST use credentials separate from the platform,
current operator authorization and bounded sessions through the audited pool-
management gateway. Before administrative effects, external audit MUST durably
record the approved actor, reason, target generation and action identity; outcomes
remain journaled under that same identity. Emergency access MUST NOT bypass fences,
make job authors operators or silently change qualification; jobs cannot invoke administration via job APIs.
Qualification: RH-T28, RH-T30.

**RH-38.** The budget owner MUST cap whole-unit CPU/memory/process/storage/I/O/
network/output and retained debt before code, including helpers, collectors and
indirect work. New identities MUST NOT reset principal/global debt. Protected
control/stop/cleanup reserve MUST remain actually usable under saturation; soft or
unestablished limits MUST NOT be reported as hard enforcement.
The resource record MUST name scheduling classes and actual placement/charging
of every attributable worker, including host-kernel work and helpers. Cgroup v2
CPU bandwidth is not a universal limit on arbitrary scheduling classes;
`memory.max` permits documented transient overshoot. The measured enforcement
envelope and usable recovery reserve MUST be published. Required headroom MUST
keep that envelope within admitted hard ceilings; otherwise admission MUST
refuse. Mechanism overshoot MUST NOT waive a requested cap or consume the
protected stop/recovery reserve.
Qualification: RH-T19, RH-T29.

## Qualification plan

These are future production-service tests on authorized pool test machines, not
executed evidence. No runtime workload, installation or service modification runs
while writing this draft. Every platform/outer-host/backend/profile path needs its
own exact production entry, identity, privileges, groups, descriptors, mounts,
environment, descendants and limits. Interactive-shell success cannot qualify it.
Every RH clause links cases below; all listed facets are required subcases.

Negatives assert the property directly and independently of other outcomes.
Read disclosure fails even with unchanged canaries. Forbidden purpose fails even
with the correct peer. Missing completion at the original bound fails even when
fenced/unknown. No unrelated branch, state or observation permits skipping these
assertions. Positive controls actually perform the permitted operation in scope.

Every completion/order subcase uses the original trigger/bound above and records
actual disposition separately from failure safety. Its named narrowing control
omits only the stated gate on a stuck/unknown branch while preserving normal
success/fences; the named witness has to fail the intended property. Each such
positive includes normal success and a transient unknown/stall resolved within the
same bound, or before the dependent event for ordering. No invented reconciliation
timeout replaces safe custody. Missing/skipped/truncated evidence is unverified;
structural document checks establish no test sensitivity or qualification.

| Case / clauses | Threats | Direct negative and required post-failure state | Actual positive | Narrowing / intended witness |
|---|---|---|---|---|
| RH-T01; RH-01/03 | TM-CRED-01, TM-CRED-02, TM-OPS-01 | Synthetic forbidden key/token/agent identity/unrelated data in host/image/backup/env/handle inventory rejects serving; untrusted broker bind/issue/renew causes zero binding/release; no secret release | Secret-free dedicated image serves with only scoped identity/configuration | Omit inventory location; HostSecretAbsent fails |
| RH-T02; RH-02/06 | TM-CRED-03, TM-AUTH-02, TM-SUP-02 | Valid pool peer at declared gateway requests platform action/foreign object/arbitrary proxy: zero protected returned bytes, repo write, signer/broker/messaging effect or onward route | Assigned scoped pool transfer/control actually completes | Remove purpose scope only, keeping peer/endpoint valid; PoolAuthorityDenied fails |
| RH-T03; RH-03 | TM-CRED-01, TM-CRED-03, TM-ISO-02 | Runner cannot read/use enrollment key or turn guest UID/job token into host/platform identity; no protected content/effect | Host reports only its scope; guest uses admitted I/O | Remove key/read or identity gate; EnrollmentKeyDenied fails independently of writes |
| RH-T04; RH-04/25 | TM-SUP-01, TM-QUAL-01, TM-CRED-01 | Self-approved/stale enrollment or copied platform secret produces no valid identity/first effect | Fresh key plus independent exact approval enrolls before first effect | Skip pending approval/publication; EnrollmentBeforeEffect fails |
| RH-T05; RH-05/06/34 | TM-QUAL-02, TM-AUTH-02, TM-NET-03 | Revoked/expired/old-generation session, retry or async completion causes zero authorized transfer/start/foreign-object access | Current scoped operation completes; renewal never revives old grant | Skip delayed final recheck; RevokedSessionDenied fails before next effect |
| RH-T06; RH-07 | TM-SUP-01, TM-QUAL-02 | Changed digest/configuration/firmware/outer host or revoked rollback cannot activate/run payload; unknown or changed mandatory CPU mitigation/firmware/microcode/SMT/KSM state denies placement | Approved exact tuple proceeds after current reviewed evidence; exact recorded mandatory mitigation state passes the same placement | Skip invalidation; ChangedTupleDenied fails; omit mitigation-state gate only; MitigationStateCurrent fails |
| RH-T07; RH-07/08/25 | TM-QUAL-01, TM-QUAL-02 | Self-reported, borrowed/shell-only/wrong-service evidence or missing parameter cannot admit work; unapproved production build/filter identities cannot qualify or serve | Independent exact service evidence permits scoped placement; approved exact production target and filter policy remain eligible for qualification | Skip pending evidence gate; QualificationBeforePlacement fails; omit build/filter identity gate only; ProductionBuildBound fails |
| RH-T08; RH-09/14/26 | TM-LIFE-01, TM-QUAL-02 | Concurrent place/drain/revoke and restore cause no post-fence placement, duplicate live scope or lost debt | Serialized placement reserves once, identical retry remains idempotent | Skip pending reservation/fence gate; OneAllocationBeforeEffect fails |
| RH-T09; RH-10/25 | TM-LIFE-01, TM-DOS-01 | Live/unknown work or incomplete cleanup/custody at B-drain fails completion despite empty queue/fences; no service/reuse | Actual absence/cleanup/custody finish by original drain bound | Omit stuck drain completion only; DrainCompletedByBound fails |
| RH-T10; RH-11/12/25 | TM-SUP-02, TM-QUAL-02 | New starts/identity effects zero at revocation; missing actual stop/qualified non-revoked narrowing at B-revoke fails; fresh guest/self-report cannot clear external fence; AD-04 dishonest local healthy/zero/cleanup reports cannot prove physical stop or release reuse; absence of independent evidence remains unknown and fails required completion | Timely disposition and independent rebuild/enrollment/qualification enable only new tuple; bounded independent mechanism/observer fixture proves actual stop; separately authorized recovery opens only a fresh generation | Keep fences, omit stuck disposition/recovery gate; RevokeDispositionByBound/RebuildBeforeReadmit fail; trust local stop evidence only while retaining external fences; IndependentStopEvidenceRequired fails |
| RH-T11; RH-12/13/25 | TM-SUP-02, TM-OPS-01 | Persistent compromise denies service; missing actual disposal/revocation by original B-retire fails even if fenced/unknown; keys/access/data stay accounted | Approved trusted recovery or replacement; disposal/revocation complete by B-retire | Omit unknown disposal completion; RetirementCompletedByBound fails |
| RH-T12; RH-14/17/20/25/26 | TM-LIFE-01, TM-RES-01 | Interrupted start/observe/restore cannot duplicate launch, signal successor, mutate via observe or infer absence from receipt | Intact original effect reconciles once before dependent reuse/release | Skip pending original-evidence gate; OriginalEffectBeforeDependentAction fails |
| RH-T13; RH-15/18/28 | TM-ISO-01, TM-DATA-01, TM-DATA-04 | HostReadDenied/LiveNeighbourReadDenied: actual global/temp/direct/link/alias/mount/handle reads deny; zero unauthorized content in responses/logs/artifacts even with unchanged files | Own-input reads return exact permitted bytes through admitted routes | Remove read denial only; preserve write denial/admission; both named read witnesses fail |
| RH-T14; RH-17/25 | TM-ISO-01, TM-SUP-01 | No controlled instruction before full boundary/receipt verification; forbidden host/neighbour writes change no bytes/metadata. Before InstanceStart, absent/wrong actual cgroup/controllers/effective limits/charged scope or unapproved production build/filter identities refuses, including cgroup-v2/no-arguments/absent-parent successful process start. Omitted main/API/vCPU/helper thread, missing/mismatched/unknown installation, debug build, `--no-seccomp`, empty required or allow-default filter, unsupported-target fallback or `/proc` mode/count used as identity (same status but different or unknown filter bytes) refuses with no guest release/partial job effect. A post-start all-filter observation cannot pass when the main VMM filter follows vCPU resume. StockColdStartPendingDenied: the pinned stock cold-start tuple refuses admission before the API because its main VMM filter follows vCPU resume and no equivalent pre-execution boundary is qualified. | Entrypoint and own-output write run only after full boundary/receipt and both phases on a separately qualified entry point: pre-API cgroup/build/filter-policy checks, then all-required-thread installation before the first guest instruction, established by reviewed source-to-binary provenance or a qualified protected verification/release sequence. Approved restrictive filter identities and complete inventory remain bound; post-start mode/count corroborates only. Valid production build/filter/provenance control with approved exact filter bytes and matching status as corroboration executes; the same control with each listed forbidden build/filter/identity/inventory/order variant must refuse. The stock execution positive is unavailable and unrun; its intended pending refusal does not substitute. | Skip pending apply/publish; BoundaryBeforeInstruction fails. Independently omit pre-API cgroup checking or pre-guest installation-order/identity checking on the qualified entry point; AppliedCgroupBeforeInstruction / AppliedSeccompBeforeInstruction must fail. Treating stock API success as the protected release is the StockColdStartPendingDenied counterexample. Independently permit debug build, disabling flag, allow-default/empty fallback, omitted thread, count-as-identity or post-start-as-order; ProductionBuildOnly / SeccompEnabled / RestrictiveFilterIdentity / ThreadInventoryComplete / FilterCountNotIdentity / FilterOrderBeforeGuest must fail respectively. All runtime controls remain proposed and unrun. |
| RH-T15; RH-17/19 | TM-ISO-02, TM-CRED-03 | Actual FD/socket set has only declared interfaces; same-peer/endpoint forbidden purpose yields no bytes/effect/spawn/onward request; guest UID gives no platform right | Allowed descriptors/purpose actually transfer data or perform scoped effect | Remove purpose scope only, retain peer/FD gates; ServicePurposeDenied fails |
| RH-T16; RH-16/24/36 | TM-DATA-05, TM-RES-02 | Authorized digest-matching unsafe paths/types/links/collisions/expansion in each outside interpreter cause no outside write/hook/discovery/partial publication/launch | Benign bounded object wholly materializes; opaque transfer invokes no outside content parser | Skip path/type/streaming gate; InterpreterEffectDenied fails |
| RH-T17; RH-11/17/20/21/25 | TM-LIFE-01, TM-QUAL-02 | Escaped/OS-spawned/helper work or incomplete account scan prevents absence; live work past B-stop fails despite unknown/fenced; no stale successor signal | Whole-account quiet-period zero and all helpers/work absent by original bound | Omit stuck full-scope termination only; WholeJobStoppedByBound fails |
| RH-T18; RH-15/28 | TM-DATA-02, TM-SIDE-01 | Incompatible sharing or writable base/cache access denies; peer bytes unreadable and immutable base bytes/metadata unchanged; revoked or digest-valid root images with unaccepted embedded IDs/seeds/credentials/prior state reject before execution; cold boot alone cannot pass | Distinct permitted units use private state/authorized immutable content; no side-channel elimination claim; accepted sanitized image meets exact early-boot entropy policy and supplies per-execution identity; small-cohort uniqueness is only a regression observation | Remove sharing/immutable scope gate; SharedBaseAccessDenied fails; omit image-state or entropy-readiness acceptance separately; ImageStateAccepted / EarlyEntropyReady fail |
| RH-T19; RH-21/25/38 | TM-DOS-01, TM-DOS-02, TM-TIME-01 | Whole-unit counters stay within ceilings; retry/clock/reboot never extends budgets; actual stop/recovery completes during saturation; arbitrary scheduling-class or uncharged kernel/helper work cannot bypass CPU budgets; measured memory overshoot cannot exceed admitted envelope or consume recovery reserve | Budgeted work and protected stop/cleanup finish within original limits; below-cap work in each allowed class and within-headroom memory pressure permit actual timely recovery | Omit stuck reserve/stop gate, keep nominal reserve/normal work; SaturatedRecoveryByBound fails; omit scheduling/charging or memory-envelope check separately; SchedulingScopeCharged / MemoryEnvelopeWithinCap fail |
| RH-T20; RH-15/23/25/27/28/29/34 | TM-DATA-01, TM-DATA-02, TM-DATA-03, TM-CRED-02 | Seed all nine classes: missing resource, prior bytes/identity/session/device state or incomplete reset at B-destroy fails cleanup and prohibits host service/reuse | All manifested classes destroyed/reset by original bound; successor reads own content and old grants reject | Skip stuck disposal per class independently; ResourceClassDestroyedByBound fails for that class |
| RH-T21; RH-23/25/27/29 | TM-LIFE-01, TM-DATA-01 | Interrupt each allocation/disposal checkpoint: unknown never counts as cleanup/absence or releases debt; host accepts no other job | Original manifest reconciles; actual cleanup finishes by original B-destroy | Mark unknown resource clean only on retry; ManifestCleanupByBound fails |
| RH-T22; RH-02/25/31/32/33 | TM-NET-01, TM-NET-02, TM-NET-03 | Outer capture and denied endpoints see no default-denied egress, unsolicited ingress or direct platform traffic, including host-origin escape routes | Allowed gateway operation and declared dependency/return traffic complete | Remove one upstream/host route gate; NetworkDestinationDenied fails |
| RH-T23; RH-25/31/32/34 | TM-NET-01, TM-NET-02, TM-QUAL-02 | DNS/redirect/family/metadata/link-local/proxy-failure routes reach no denied endpoint; old route/session closes before next forbidden effect and by B-network | Allowed chain works; failure closes path in time; fresh generation gets own bound policy only | Skip stuck route/session closure; RouteClosedBeforeEffect/NetworkIsolationByBound fail |
| RH-T24; RH-22/23/25/30 | TM-LIFE-01, TM-RES-01, TM-DATA-01 | Missing blob/digest-only custody cannot authorize source deletion, clean/reusable claim or whole-result success | Actual complete blobs retrievable outside deletion scope or exact discard before deletion | Skip pending blob-custody gate; ContentBeforeDeletion fails |
| RH-T25; RH-22/24/25 | TM-RES-01, TM-RES-02, TM-DOS-02 | Missing dependency/binding or invalid result produces no whole verified publication; valid complete intake unknown past B-intake fails separately from quarantine | Admitted benign complete provenance/blobs actually publish wholly by original bound; invalid sibling terminal-refuses safely | Omit stuck intake completion only; IntakeCompletedByBound fails |
| RH-T26; RH-06/24/26 | TM-RES-03, TM-QUAL-01, TM-RES-02 | Host-signed false execution/cleanup never establishes independent truth/attestation; instruction-like report text causes no execution/discovery/authority change; under AD-04 a signed local zero/cleanup report cannot satisfy independent stop completion or authorize reuse | Exact report retained as host claim; separate independent evidence supports only its stated scope; independent external stop evidence is accepted only for its qualified physical scope | Promote report or execute returned text; HostClaimNotAuthority fails; promote local report to stop evidence only; IndependentStopEvidenceRequired fails |
| RH-T27; RH-13/25/30 | TM-OPS-01, TM-CRED-02 | Refused retrieval is insufficient: covered log/artifact/backup bytes present after B-delete fail deletion, retain debt and never waive custody | Covered-copy deletion/evidence complete by original bound; authorized pre-expiry retrieval works | Omit stuck deletion, keep access denial; CoveredCopiesDeletedByBound fails |
| RH-T28; RH-33/35/36/37 | TM-AUTH-01, TM-AUTH-02, TM-ISO-02 | Job/author host shell/debug or privileged path/command/template/device request rejects with zero host process/mount/platform effect | Same admitted workload runs inside unit; qualified typed host action uses operator-owned handles | Ignore argument/action scope; HostPrivilegeDenied fails |
| RH-T29; RH-35/36/38 | TM-DOS-01, TM-AUTH-01, TM-QUAL-01 | Missing delegation, unknown helper privilege or soft limit cannot silently launch unbounded/privileged work; actual helpers accounted | Qualified least-privileged service applies measured caps and required typed setup/stop | Omit helper accounting/permission gate; WholeUnitPrivilegeAndLimitDenied fails |
| RH-T30; RH-30/37 | TM-OPS-01, TM-CRED-01, TM-AUTH-02 | Unauthorized operator/job gets no admin session/retained bytes; missing actor/generation/reason prevents successful admin effect; no payload hidden in audit exception | Authorized bounded pool action executes with external audit; scoped retrieval respects expiry without platform credential | Omit current admin/audit gate; AuditedPoolAdminOnly fails |

## Security limits and activation dependencies

The host kernel, backend/VMM, loader, privileged helpers and local cleanup observers
are the local TCB. Independent enrollment, gateway scope, upstream policy and
external fences limit what a stolen host identity can do to the platform; they do
not prevent a compromised host from reading assigned job data or forging results.
Its local stop/reset claim can also be false. Missing actual timely disposition
fails qualification even when independent isolation contains the immediate damage.

Unknown escapes, physical/firmware attacks and residual side channels are not
eliminated by a finite suite. Job authors trust disclosed operator access; no
confidential-computing or hardware-attestation property is supplied here. Initial
verification stays secret-free; admitting job-supplied sensitive material would
require a separately approved class, not a pool flag or declaration field.
Platform-host execution restrictions and station/workplace boundaries stay intact.

Activation depends on independent fact checking of the provisional
[backend choice](../research/backend-choice.md), its owner decisions, published
companion contracts and exact host admission/qualification. The initial cold
Firecracker/bare-metal/exclusive binding supplies no deployment evidence by itself;
the required owner decisions below still gate service. A draft,
structural checker, installed helper or successful guest boot cannot activate a
pool host. Unsupported capabilities remain refusals rather than implicit fallback.
The RH-17 stock cold-start main-filter-after-resume ordering MUST block qualification and activation;
no installed production binary, internal TLS barrier or successful boot can close
it. A separately qualified entry point and its actual execution positive are
required before this mandatory property can become available.

## Open decisions for the owner

Choices below gate service activation. Recommendations are not assumed decisions;
a job declaration or host report cannot supply an unresolved answer.

1. **Q-RH-01 — Operator.** Who provisions and responds to incidents?
   Recommended: a dedicated pool operations role, separate credentials, external
   audit custody and an independent qualification acceptor.
2. **Q-RH-02 — Substrate.** Physical or rented machines, with which ownership?
   Recommended: platform-owned dedicated physical machines initially; any rented
   infrastructure needs an explicit ownership/control, visibility and outer-host
   trust decision rather than silent substitution of the separate-machine model.
3. **Q-RH-03 — Post-compromise reuse.** What permits return to service?
   Recommended: independent trusted reprovision, fresh enrollment/qualification;
   replace/retire where firmware or outer-host trust cannot be re-established.
4. **Q-RH-04 — Retention.** Which log/artifact durations, copies and access?
   Recommended: minimal bounded retention, separate metadata/content scope,
   disclosed operator access and actual covered-copy deletion evidence.
5. **Q-RH-05 — Identity/gateway protocol.** Who issues/revokes which purposes?
   Recommended: independent pool-only authority, final currentness verification
   and exact action/object scope; never copy a platform credential.
6. **Q-RH-06 — Cleanup and bounds.** Which reset/erase standards, quiet period,
   stop/drain/revoke/destroy/intake/delete/retire/network bounds and reserve?
   Recommended: refuse service until published and measured/qualified for every
   resource class under the exact backend and production-service tuple.
7. **Q-RH-07 — Backend/sharing.** Which exact tuple/device and sensitivity policy activates?
   Recommended provisional target: one cold Firecracker microVM under the jailer
   per job, one live job per dedicated bare-metal physical host, devices denied
   initially and no automatic fallback, per the [backend choice](../research/backend-choice.md).
   Independent fact checking, owner decisions and exact-tuple qualification remain
   pending; disclose residual side channels rather than promising elimination.
8. **Q-RH-08 — Emergency access.** Which administration and break-glass scope?
   Recommended: audited pool-only gateway sessions under current operator
   authority; no hidden direct login, reusable platform credential or fence bypass.
