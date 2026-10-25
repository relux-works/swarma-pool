# Public execution pool threat model

Status: Draft (2026-10-25).

Proposed security requirements; no pool activation or qualification claim.
This reference gives downstream documents stable threat and control identifiers.
A required control is an obligation to specify, implement and qualify, not evidence
that a backend already provides it. Identifier meanings remain stable across edits;
retired identifiers are retained as retired rather than reassigned.

## Scope and security posture

The public pool runs arbitrary third-party code on dedicated platform-owned
machines. A runner means one ephemeral isolated execution, not a persistent agent
station or an account reused as an execution boundary. Assume the payload is
already hostile; reviewing its source is not an isolation control.

The desired boundary contains a hostile payload, limits damage to other jobs and
the network, and prevents a compromised pool host from acquiring platform
secrets or authority. It does not make a dishonest host's computation truthful.
A pool machine contains no platform secrets, subscription credentials, platform identity
keys, infrastructure credentials or authority to act as a trusted platform peer.
Its management and result interfaces are separate, narrowly mediated surfaces.

A proposed pool-specific authentication mechanism must have no platform-service,
station, broker, registrar or key-custody authority. Its exact design is an open
activation gate (Q-TM-01). Public verification keys are not secrets. A host's
signature, if introduced, authenticates its report only; it is not platform
endorsement, hardware attestation or proof of execution.

### Components and placement

| Component | Scope and trust |
|---|---|
| Job submitter | Untrusted principal and declarations; authenticated admission does not make code safe. |
| Scheduler and dispatcher | Trusted admission, placement, budgets, lifecycle and request binding; outside pool hosts. |
| Credential broker | Trusted credential boundary outside the pool; no untrusted subscription binding or lease. |
| Key custody and registrar | Trusted identity infrastructure outside the pool; no sockets, keys or proxy access from runners. |
| Messaging and authority verification | Transport carries data; protected consumers check signatures, grants, audience and current authority. |
| Pool host | Dedicated machine; host supervisor is trusted for local containment but may be compromised or dishonest towards the platform. |
| Runner | One fresh isolated execution of untrusted code, including startup scripts, dependencies and subprocesses. |
| Artifact and input store | Separate trusted authorization and bounded transfer endpoints; all job-origin objects remain untrusted. |
| Result return path | Bounded intake, quarantine and validation outside execution; never an instruction or execution path. |
| Network egress | Independently enforced policy, DNS mediation and accounting; no general platform network route. |

The host supervisor, image loader, backend, virtual-device interfaces and cleanup
logic are attack surfaces even when the payload never runs privileged. Every
pre-start materializer and post-stop collector processes hostile bytes.

```text
[untrusted submitter] -- B1: bounded job request --> [trusted admission/control]
                                                        |
                    broker / keys / registrar / messages | no runner access
                                                        |
                                  B2: admitted job + pinned policy
                                                        v
 +---------------- dedicated pool host -------------------------------+
 | [host supervisor + backend] <--- B3 ---> [ephemeral hostile runner] |
 |       no platform secrets                    |                    |
 +----------------------------------------------|--------------------+
                                                |
                           B4: scoped reads/writes to transfer gateway
                                                v
                             [input/artifact store + result quarantine]
                                                |
                           B5: validated inert data to requester/review
                                                v
                                      [trusted result consumer]
 [runner] -- B6: enforced DNS/egress gateway --> [untrusted network]
 [qualification authority] -- B7: admitted tuple --> [scheduler + host]
 [job] -- B8: shared hardware / sequential reuse --> [other jobs]
```

B2 is a trust boundary even with a platform-owned host: a host can lie. B4 and
B5 remain boundaries after transport authentication or content hashing. Network
policy must also cover host-origin traffic after a runner escape, not only B6.
Management-plane reachability is distinct from payload egress.

### Existing boundaries preserved

- Dispatcher R-VERIFY requires builds, tests and other worker-code execution on
  another machine, in ephemeral runners without secrets and with controlled
  dependency egress. A station orchestrates verification and reads its results.
- Dispatcher R-EXEC restrictions on agent hosts are not relaxed by pool capacity.
  R-SHARED deployment isolation and R-NETBIND/R-NET-TOOL remain in force there.
- Slice 0 refuses the untrusted class. This model enables no existing account,
  workplace, network mode, job class or unpublished wire format.
- Broker R-UNTRUSTED forbids bindings, issuance and renewal for untrusted accounts.
  A caller label, trusted-circle association or signed job request cannot waive it.
- A guest UID is not a host peer UID. Neither matching numbers nor a forwarded
  socket establishes the registrar or keymaker module's host-local peer identity.
- CIP-0024 stations have persistent identity, protected agent controls and their
  own authority lifecycle. Those station properties are outside pool execution.
  A pool runner gets no station signer, agent loop or provider credential.

Backend selection, concrete wire schemas, numerical quotas, deployment commands
and production qualification belong to the downstream documents. This model
requires properties without ranking or selecting isolation products.

## Assets

| ID | Asset and required property |
|---|---|
| AS-01 | Platform secrets and authority: absent from pool hosts, runners, images, backups and logs. |
| AS-02 | Control-plane availability and integrity: job or host failures cannot gain control or consume protected recovery capacity. |
| AS-03 | Other jobs' inputs and outputs: private authorization and no cross-job read, write or substitution. |
| AS-04 | Job-supplied secrets: initial verification forbids them; sensitive bytes must not accidentally enter images, caches, results or later jobs. |
| AS-05 | Result integrity towards the requester: exact provenance and freshness, with truth claims limited to their evidence. |
| AS-06 | Pool host integrity across jobs: fresh execution state and explicit recovery after suspected host compromise. |
| AS-07 | Network reputation and external systems: pool addresses cannot become unrestricted abuse or exfiltration infrastructure. |
| AS-08 | Operator data on pool hosts: no unrelated personal, operational or platform data; minimal dedicated management state protected from jobs. |
| AS-09 | Qualification records: authentic scope, current tuple, revocation and negative evidence; no self-asserted pass as admission. |
| AS-10 | Software and policy provenance: exact admitted inputs, backend, runner image and enforcement policy. |

## Adversaries

| ID | Capabilities | Goals and trust consequence |
|---|---|---|
| AD-01 hostile job code | Arbitrary guest computation, malformed files/syscalls/protocol bytes, subprocesses and races within offered interfaces. | Escape, read data, persist, spoof output, exhaust resources or abuse the network. Assume compromise from the first instruction. |
| AD-02 hostile job author | Repeated submissions, misleading declarations, chosen inputs, dependency/image references, result text and multiple identities where admission allows them. | Obtain stronger placement, flood admission, exploit collectors/reviewers and turn valid submission authority into platform authority. |
| AD-03 neighbouring job | Concurrent shared-hardware observations or later execution on the same host; chosen cache and storage content. | Read or poison another job's state through direct access, residue or side channels. |
| AD-04 compromised or dishonest host | Control supervisor, host kernel, storage and reported clocks/results; observe guest memory; sign whatever its own reporting identity permits. | Steal job data, forge execution/cleanup/qualification reports, persist and attack platform-facing interfaces. Ownership does not establish honesty. |
| AD-05 network adversary | Observe, delay, replay or alter unauthenticated traffic; supply DNS responses and remote endpoints; send ingress and attract egress. | Substitute inputs/results, reach protected services or use pool traffic for external abuse. |
| AD-06 platform and operators | Control admission, images, hardware, host administration, stores and delivery; may inspect data or stop service. | From a job author's perspective, can observe or alter execution. The service must disclose this trust and retention scope; no confidential-computing claim is made. |

## Specification carriers

In this document, **contract** means the proposed foreign-code execution CIP,
built on the general Execution Isolation Contract. **Backend choice** means its
isolation evaluation; **runner hosts** means dedicated-host operating rules;
**admission/qualification** means host enrollment, capability evidence and scheduling gates. These are
responsibility names, not claims that a numbered proposal or wire schema exists.

## Interpretation inventory

Prefer opaque transfer: the host passes bounded authenticated bytes and interprets
job-controlled formats inside an already isolated runner. This is a design
preference, not an assumption that a host never parses anything. Each unavoidable
outside interpreter must be explicitly admitted, enforce its registered schema
and limits, and refuse unsupported forms before publication or launch. TC-25 and
TM-DATA-05 cover the external interpretation class, including preparation before
there is a runner; integrity and authorization alone do not establish safe shape.

| ID | Interpretation point | Enforcing component | Location relative to runner | Controls | Threats |
|---|---|---|---|---|---|
| IP-01 | Submission, launch manifests and authority envelopes | Admission verifier and scheduler | Outside; registered bounded schemas only | TC-12, TC-20, TC-21 | TM-AUTH-02, TM-DOS-02 |
| IP-02 | Input fetch, object identifiers and transfer framing | Authorized transfer gateway | Outside; object content opaque where possible | TC-12, TC-21, TC-25 | TM-DATA-04, TM-DATA-05 |
| IP-03 | Unpack and input-root materialization | Runner-side materializer or approved constrained preparation worker | Inside preferred; external worker subject to TC-25 | TC-25 | TM-DATA-05 |
| IP-04 | Image manifests, layers and assembly | Approved image loader and constrained assembler | Outside for approved image loading; job formats inside or separately admitted under TC-25 | TC-13, TC-25 | TM-SUP-01, TM-DATA-05 |
| IP-05 | Dependency fetches, DNS, redirects and protocol metadata | Egress proxy and scoped transfer gateway | Outside for destinations/framing; content opaque | TC-11, TC-12, TC-15, TC-25 | TM-NET-02, TM-DATA-05, TM-RES-01 |
| IP-06 | Dependency resolution, package extraction and install hooks | Job tooling under the applied backend | Inside only; no host package-manager execution on behalf of the job | TC-02, TC-15, TC-25 | TM-ISO-01, TM-RES-01, TM-DATA-05 |
| IP-07 | Guest I/O, backend/device messages and guest status bytes | Supervisor and qualified backend adapter | Outside; declared bounded interfaces only | TC-02, TC-03, TC-20, TC-25 | TM-ISO-01, TM-ISO-02, TM-DOS-02, TM-DATA-05 |
| IP-08 | Logs, telemetry, error strings and displays | Bounded telemetry collector and inert renderer | Outside; data cannot select commands or metric schemas | TC-16, TC-20, TC-25 | TM-RES-02, TM-DOS-02, TM-DATA-05 |
| IP-09 | Result intake, artifact unpack and publication | Collector, quarantine validator and importer | Outside; atomic validated publication only | TC-16, TC-20, TC-25 | TM-RES-02, TM-DATA-05 |
| IP-10 | Cancellation, observation and status requests | Lifecycle controller and action verifier | Outside; identity/generation-bound commands only | TC-12, TC-21, TC-22 | TM-AUTH-02, TM-LIFE-01 |
| IP-11 | Host qualification reports and health assertions | Independent qualification/admission gate | Outside; reports do not authorize themselves | TC-17, TC-23 | TM-QUAL-01 |
| IP-12 | Cleanup, export recovery and retained path metadata | Constrained collector and lifecycle reconciler | Outside; paths remain untrusted across retries | TC-22, TC-25 | TM-LIFE-01, TM-DATA-05 |
| IP-13 | Candidate inspection and report/repository rendering | Result consumer and review adapter | Outside; inert resources with discovery excluded | TC-16, TC-21 | TM-RES-02 |

Every inventory row has controls and threats in the hand-off table. New external
interpretation points require an inventory entry, enforcing component, bounded
schema/format and paired qualification case before admission. No catch-all shell,
ambient hooks, plugins or repository discovery is an allowed external interpreter.

## Control vocabulary

Controls below are proposed requirements. The hand-off table names their primary
carrier; companion specifications own their existing service semantics. Numerical
limits and evidence encodings must be published before admitting jobs.

| ID | Required property |
|---|---|
| TC-01 | Dedicated pool machines and network segmentation; no platform secrets, authority, services or unrelated operator data on them. |
| TC-02 | Backend boundary applied before any payload code; minimum privileges, devices, kernel/VMM interfaces and host access; refuse unsupported capability combinations. |
| TC-03 | No host service sockets, management endpoints, inherited descriptors or host namespaces exposed to jobs; explicit minimal guest I/O only. |
| TC-04 | Fresh per-execution storage and memory; verified destruction/absence before reuse; unknown cleanup retains quarantine. |
| TC-05 | No shared mutable job caches; shared objects, including runtime dependencies, immutable, digest-verified, read-only and authorized by scope. |
| TC-06 | Devices denied by default; qualify state reset and DMA isolation before admitting accelerators or passthrough. |
| TC-07 | Explicit concurrency and side-channel policy; refuse placements outside the approved sharing class and disclose residual leakage. |
| TC-08 | No platform credential provisioning and no broker binding/lease; verification is secret-free, including job-supplied secrets by default. |
| TC-09 | Separate host/guest identity and authority; no signer or registrar socket forwarding, guest-UID translation or borrowed station identity. |
| TC-10 | Default-deny egress with externally enforceable containment, scoped dependency acquisition, bandwidth/connection bounds and abuse response. |
| TC-11 | Controlled DNS and destination validation for every connection/redirect; block private, loopback, link-local, metadata and platform ranges across address families. |
| TC-12 | Authenticated, integrity-protected bounded transfers, replay protection and no unsolicited job ingress; no reliance on network location as authority. |
| TC-13 | Approved digest-pinned images, backend and policies; verified provenance, safe image/layer preparation under TC-25, controlled updates and rollback denial for revoked tuples. |
| TC-14 | Suspected host compromise fences all placement; external revocation and trusted rebuild/re-enrollment before reuse. |
| TC-15 | Bind results to admitted request, requester, exact inputs including the complete runtime dependency manifest, execution attempt, host/boot generation, policy and output digests; reject duplicates and stale attempts. |
| TC-16 | Bounded atomic result/log/telemetry intake and inert consumption; safe paths/types, no hooks, discovery, active rendering or automatic execution; external interpretation also meets TC-25. |
| TC-17 | State result evidence level; independently trusted verification for claims beyond a host report; no truth or attestation inference from signatures. |
| TC-18 | Trusted external authority time and monotonic execution budgets; guest/host claims cannot extend grants, qualification or deadlines. |
| TC-19 | Bound CPU, memory, processes, disk, I/O, output and runtime before execution; preserve host stop/observe/cleanup capacity. |
| TC-20 | Admission, transfer and result budgets per principal and globally; bounded parsing/queues and protected control/recovery capacity. |
| TC-21 | Authenticated recorded admission class; complete policy intersection, capability matching and action-specific attenuated current authority. |
| TC-22 | Durable lifecycle fencing and execution identity; no retry/reuse while launch, stop, teardown or export is unknown. |
| TC-23 | Independently reviewed qualification tied to an exact tuple; negative and positive evidence, freshness, revocation and change invalidation. |
| TC-24 | Disclose operator access, sensitivity exclusions and retention; enforce authorized store retrieval/deletion and auditable administrative access. |
| TC-25 | Every external job-byte interpreter enforces admitted schemas, paths/types and streaming byte/count/expansion limits; materialization uses private constrained staging; no root escape, ambient behavior, partial publication or unauthorized external effects; opaque content stays uninterpreted outside the runner. |

### Input preparation and dependency provenance

For unavoidable external interpretation, the named preparation worker or receiving
service refuses absolute/traversing/ambiguous or colliding paths, unsupported
entry types, links escaping the admitted root, devices and unsupported link forms.
No link following or ambient code-loading is permitted. Bounds apply while reading
and expanding, before allocation or writes can exceed the admitted limits. For
materialization, use a fresh private staging root with no platform access;
validate the complete bounded object before atomic publication into the admitted input/output area. Refusal
publishes no partial object, changes no pre-existing input or unrelated path and
starts no payload. Discard/quarantine staging through the fenced lifecycle;
interruption never converts staging into accepted input. Authorized bounded
fetches and refusal telemetry remain permitted, never arbitrary external writes.

Runtime-resolved or fetched dependencies are part of result input provenance.
TC-15 requires a complete manifest of actual content digests, resolved identities
and resolution metadata for every consumed dependency, including transitive
fetches, bound to the execution and returned result. An endpoint or package name
alone is insufficient. Opaque fetches can record exact byte identities without
host unpacking. The result consumer refuses a successful verified-result claim
with missing or incomplete dependency provenance; it may retain diagnostic data
as explicitly incomplete. A changed dependency produces a different manifest,
even at the same allowed endpoint/name. This records what was consumed, not
whether the dependency was benign. On a dishonest host the manifest remains a
host report under TC-17; recording it supplies no independent execution proof.

## Threat catalogue and qualification obligations

Each negative test states an intended rejection/invariant and a neighbouring
positive control. These are future executable qualification cases, not executed
attacks or claims of passing runtime tests. Run them only in isolated fixtures.
Names are stable test references for downstream evidence. After refusal, assert
no unauthorized read/write, launch, credential release or accepted result as
applicable, and retain recovery evidence. A timeout or crash alone is no pass.

### TM-ISO-01 — Payload escapes into the host kernel or VMM

- Adversary: AD-01; boundary: B3; assets: AS-06, AS-03, AS-02.
- Impact: kernel/VMM or supervisor compromise exposes all local jobs and enables
  attacks on the control plane despite guest isolation.
- Required controls: TC-01, TC-02, TC-13, TC-14.
- Carrier: backend choice and runner hosts; contract fixes minimum capabilities.
- Negative test `iso-host-boundary`: bounded probes of forbidden host mounts,
  privileges and interfaces cannot modify a host canary or obtain a host handle;
  an unsupported required isolation capability refuses before payload launch.
- Positive control: an admitted ordinary workload reads its own inputs, writes
  its own output and exits under the same profile. Probes do not prove no exploit.

### TM-ISO-02 — Job reaches host services or control sockets

- Adversary: AD-01; boundary: B3; assets: AS-01, AS-08.
- Impact: management, signing, runtime or metadata services become confused
  deputies through inherited descriptors, mounts, proxying or host networking.
- Required controls: TC-01, TC-03, TC-09, TC-11.
- Carrier: runner hosts and backend choice; existing registrar/keymaker module specs.
- Negative test `iso-service-denial`: attempts at fixture host sockets, management
  addresses and inherited handles refuse with no host-side effect or signature.
- Positive control: the explicitly admitted guest output channel works and
  cannot select another endpoint or arbitrary host path.

### TM-DATA-01 — Sequential disk or memory residue crosses jobs

- Adversary: AD-03; boundary: B8; assets: AS-03, AS-04, AS-06.
- Impact: a later execution reads prior files, scratch disks, swap, memory,
  snapshots or crash dumps; early reuse races surviving processes.
- Required controls: TC-04, TC-22, TC-24.
- Carrier: runner hosts; contract defines absence, cleanup and retention semantics.
- Negative test `data-sequential-residue`: seed synthetic markers in prior-job
  state; the next job cannot read them. Interrupt cleanup and require quarantine,
  with no next launch and no silent success receipt.
- Positive control: after verified teardown a fresh job runs and retained outputs
  remain available only through the previous requester's authorized store path.

### TM-DATA-02 — Shared cache poisons or discloses another job

- Adversary: AD-01, AD-03; boundary: B4/B8; assets: AS-03, AS-05, AS-10.
- Impact: writable tool/dependency caches inject code or reveal private cache
  entries, including digest-addressed objects not authorized for that requester.
- Required controls: TC-05, TC-12, TC-21.
- Carrier: contract and runner hosts; artifact-store authorization specification.
- Negative test `data-cache-scope`: writes to shared objects, substituted digests
  and reads of another requester's private cached object refuse without mutation.
- Positive control: an authorized immutable public dependency is served by exact
  digest; each job can write a separate private cache that is destroyed on teardown.

### TM-DATA-03 — Device state or DMA survives the runner

- Adversary: AD-01, AD-03; boundary: B3/B8; assets: AS-03, AS-06.
- Impact: accelerator memory, firmware state or passthrough DMA bypasses the
  boundary or leaks data after nominal runner destruction.
- Required controls: TC-02, TC-04, TC-06, TC-23.
- Carrier: backend choice and admission/qualification; runner hosts own reset.
- Negative test `data-device-reset`: an unqualified device request refuses before
  launch; for a qualified device, a prior synthetic marker is unreadable after
  reset, and an interrupted reset prevents reuse.
- Positive control: a device-free admitted job runs; any later device-enabled
  positive case must match the exact reset/DMA qualification tuple.

### TM-DATA-04 — Input or artifact store becomes a cross-job deputy

- Adversary: AD-02, AD-04; boundary: B4; assets: AS-03, AS-05.
- Impact: guessed object identifiers, changed paths or overwritten objects expose
  other jobs' inputs/results or substitute a different admitted input.
- Required controls: TC-05, TC-12, TC-15, TC-21, TC-24.
- Carrier: contract and existing artifact/input-store specification.
- Negative test `data-object-authority`: foreign-object reads/writes and content
  differing from the admitted digest refuse; guessing a digest grants no access.
- Positive control: the authorized exact input and complete scoped output upload
  succeed without granting enumeration, overwrite or cross-job privileges.

### TM-DATA-05 — External interpretation of hostile job bytes escapes its scope

- Adversary: AD-01, AD-02, AD-04, AD-05; boundary: B1/B2/B3/B4/B5;
  assets: AS-01, AS-02, AS-03, AS-06, AS-10.
- Impact: authorized digest-matching inputs, layers, fetched objects or telemetry
  exploit an outside interpreter before/after execution; path/type confusion,
  expansion or ambient hooks cause host writes, partial state or code activation.
- Required controls: TC-02, TC-12, TC-13, TC-16, TC-19, TC-20, TC-22, TC-25.
- Carrier: contract defines the interpretation inventory and TC-25; runner hosts
  name each enforcing preparation/transfer/collector service; backend choice and
  admission/qualification bind its isolation, limits and supported formats.
- Negative test `data-external-interpretation`: with valid authority and matching
  digests, separately plant traversing/colliding paths, root-escaping links,
  devices, unsupported types and byte/count/expansion excesses in each applicable
  external interpreter. Each refuses with no outside-root write, partial published
  input, ambient execution or payload launch; interrupted staging stays unaccepted.
  For opaque-only paths, a controlled interpretation trap plus host-side observation
  must show no host unpacker, resolver hook or content-selected helper invocation.
- Positive control: a neighbouring authorized benign bundle of the same supported
  format materializes wholly within its input root, then the job starts. Opaque
  mode transfers the same bytes unchanged and interprets them only after runner
  isolation; bounded legitimate telemetry still reaches its inert consumer.

### TM-SIDE-01 — Concurrent jobs leak through shared hardware

- Adversary: AD-03; boundary: B8; assets: AS-03, AS-04.
- Impact: shared CPU caches, memory bandwidth, scheduling or device contention
  reveals data or workload shape even without an ordinary memory read.
- Required controls: TC-06, TC-07, TC-21, TC-23.
- Carrier: contract defines accepted leakage; backend choice and qualification
  define allowed co-tenancy and hardware restrictions.
- Negative test `side-placement-class`: incompatible sharing/sensitivity requests
  cannot co-schedule, including simultaneous admission races.
- Positive control: compatible non-sensitive jobs run under the approved sharing
  policy. This tests placement only, not absence of microarchitectural leakage.

### TM-CRED-01 — Platform secrets or authority reach the pool

- Adversary: AD-01, AD-04; boundary: B2/B3; assets: AS-01, AS-02.
- Impact: images, bootstrap scripts, environment, logs, metadata or backups leak
  platform tokens, keys or transitive platform-service access.
- Required controls: TC-01, TC-03, TC-08, TC-09.
- Carrier: runner hosts and contract; broker and dispatcher retain R-UNTRUSTED.
- Negative test `cred-platform-absence`: fixture platform-secret injection and an
  untrusted bind/issue/renew request refuse with no credential release; audit
  image, environment, storage and log fixtures for seeded forbidden markers.
- Positive control: secret-free execution uses public inputs and public verification
  keys successfully. Marker checks are bounded coverage, not universal absence proof.

### TM-CRED-02 — Job-supplied secrets leak through code or retention

- Adversary: AD-01, AD-03, AD-04; boundary: B1/B4/B8; assets: AS-04.
- Impact: tokens hidden in inputs, output logs or cache layers escape through
  admitted egress or persist after the job; a host can read plaintext guest data.
- Required controls: TC-04, TC-08, TC-10, TC-24.
- Carrier: contract and runner hosts; admission/qualification fixes sensitivity.
- Negative test `cred-secret-refusal`: a declared secret-bearing verification job
  or request for credential injection refuses; no payload launches or secret is
  copied into images/logs. Use synthetic values, never live credentials.
- Positive control: the neighbouring secret-free job is admitted. Undeclared
  secrets cannot reliably be discovered; authors must not submit them.

### TM-CRED-03 — Guest impersonates a host or trusted station

- Adversary: AD-01, AD-04; boundary: B2/B3; assets: AS-01, AS-09.
- Impact: a matching guest UID, claimed executable or forwarded connection obtains
  a registrar identity, key signature or trusted message role.
- Required controls: TC-01, TC-03, TC-09, TC-21.
- Carrier: existing registrar and keymaker module specs; contract and runner hosts.
- Negative test `cred-guest-not-peer`: a guest claiming the host UID/name cannot
  register or sign; an intermediary cannot substitute its host peer authority.
- Positive control: the service's separately authorized native peer fixture
  authenticates under its own generation; it grants the guest no delegated access.

### TM-NET-01 — Egress enables abuse or exfiltration

- Adversary: AD-01, AD-02, AD-04; boundary: B6 and host uplink; assets: AS-07, AS-03.
- Impact: spam, scanning, mining traffic, arbitrary tunnels or data extraction
  damage external systems and network reputation, including after host compromise.
- Required controls: TC-01, TC-10, TC-19, TC-20.
- Carrier: runner hosts and contract; admission/qualification owns policy tests.
- Negative test `net-egress-fail-closed`: denied destinations/protocols and traffic
  after policy-proxy failure cannot leave; a compromised-host fixture cannot
  bypass the independently controlled upstream policy.
- Positive control: a pinned allowed dependency transfer succeeds within budget;
  abuse tests use controlled endpoints, never external scanning targets.

### TM-NET-02 — DNS, redirects or metadata bypass destination policy

- Adversary: AD-01, AD-05; boundary: B6/B3; assets: AS-01, AS-07.
- Impact: rebinding, alternate address families, encoded IPs, resolver channels
  or redirect chains reach platform, private, loopback or metadata endpoints.
- Required controls: TC-03, TC-10, TC-11, TC-12.
- Carrier: runner hosts; contract defines destination and DNS policy.
- Negative test `net-destination-recheck`: controlled DNS answers changing to a
  forbidden address, redirects there and alternate resolver attempts refuse;
  verify no forbidden endpoint receives a request, including IPv6 variants.
- Positive control: resolution and redirects that remain wholly inside the
  admitted dependency policy complete; an allowed name alone is insufficient.

### TM-NET-03 — Network substitution, replay or unsolicited ingress

- Adversary: AD-05; boundary: B2/B4/B6; assets: AS-03, AS-05, AS-06.
- Impact: an on-path peer substitutes inputs/results, replays a launch or reaches
  exposed runner listeners; a valid encrypted connection is mistaken for authority.
- Required controls: TC-10, TC-12, TC-15, TC-21.
- Carrier: contract and runner hosts; existing message/envelope verifier.
- Negative test `net-transfer-auth`: wrong endpoint identity, altered object,
  replayed transfer/launch and unsolicited ingress refuse without admitted effects.
- Positive control: a fresh authorized transfer of the exact object succeeds;
  duplicate retries return the recorded disposition without a second launch.

### TM-SUP-01 — Image, backend or update supply chain is substituted

- Adversary: AD-02, AD-04, AD-05; boundary: B2/B7; assets: AS-06, AS-10.
- Impact: mutable tags, poisoned toolchains or revoked versions execute attacker
  code outside the payload boundary or weaken enforcement before first launch.
- Required controls: TC-02, TC-05, TC-13, TC-23.
- Carrier: backend choice and runner hosts; admission/qualification owns tuple pins.
- Negative test `sup-pin-and-revocation`: changed bytes behind an admitted name,
  unapproved image/backend digest and rollback to a revoked tuple refuse before
  launch; job-controlled image hooks never run in the host preparation context.
- Positive control: the current approved exact tuple launches the ordinary fixture;
  dependency/install hooks execute only inside the already-established runner;
  runtime-fetched byte identities enter the complete TC-15 dependency manifest.

### TM-SUP-02 — Host compromise persists across otherwise fresh jobs

- Adversary: AD-01, AD-04; boundary: B3/B8/B7; assets: AS-06, AS-09.
- Impact: modified supervisor, boot chain, firmware or persistent services survive
  runner teardown, steal later jobs and manufacture healthy reports.
- Required controls: TC-01, TC-13, TC-14, TC-23.
- Carrier: runner hosts and admission/qualification.
- Negative test `sup-compromise-quarantine`: externally recorded compromise or a
  revoked host generation blocks new placement despite host-reported health and
  a fresh runner; ordinary teardown cannot clear the fence.
- Positive control: independently authorized rebuild/re-enrollment restores only
  the reviewed new generation. Reinstallation alone proves no firmware recovery.

### TM-RES-01 — Result forgery, substitution or replay

- Adversary: AD-02, AD-04, AD-05; boundary: B4/B5; assets: AS-05.
- Impact: a previous pass, altered artifact or another requester's result is
  accepted for the wrong input, attempt, policy or execution generation.
- Required controls: TC-12, TC-15, TC-18, TC-22.
- Carrier: contract; result consumer and existing envelope/replay semantics.
- Negative test `res-exact-binding`: change one bound identity/digest at a time,
  replay a retired attempt or substitute a blob; reject without candidate update
  or releasing another requester's content. Missing runtime dependency provenance
  cannot yield a successful verified-result claim. Serve changed dependency bytes
  at the same allowed endpoint/name: the manifest and bound input identity must
  change, rather than inheriting the previous result's provenance.
- Positive control: the exact current complete result is accepted once; an identical
  authorized retry is idempotent rather than a second logical completion.

### TM-RES-02 — Result bytes become instructions or executable behavior

- Adversary: AD-01, AD-02, AD-04; boundary: B4/B5; assets: AS-01, AS-02.
- Impact: traversal, symlinks, special files, expansion bombs, active report
  content, terminal escapes or candidate configuration compromise intake/review.
- Required controls: TC-16, TC-19, TC-20, TC-21.
- Carrier: contract and result consumer; dispatcher R-TRANSFER and review rules.
- Negative test `res-inert-intake`: fixture unsafe paths/types, over-limit data,
  hooks, instruction-like text and active markup cause refusal or inert rendering
  as specified, with no partial import, execution, discovery or authority change.
- Positive control: a bounded ordinary artifact and harmless instruction-looking
  text can be inspected as data; code runs only in a separately admitted runner.

### TM-RES-03 — A dishonest host signs a false successful execution

- Adversary: AD-04; boundary: B2/B5/B7; assets: AS-05, AS-09.
- Impact: syntactically valid signed output fabricates tests, resource use or
  isolation application; authenticity is presented as honest computation.
- Required controls: TC-15, TC-17, TC-23.
- Carrier: contract and admission/qualification; result consumer states evidence.
- Negative test `res-report-not-proof`: a valid fixture host signature over a
  fabricated pass never satisfies a policy requiring independent trusted
  verification or hardware attestation; no automatic integration follows.
- Positive control: the same report may be recorded as host-reported evidence;
  separately obtained verification satisfies only its explicitly stated scope.

### TM-TIME-01 — Guest or host clock manipulation revives authority

- Adversary: AD-01, AD-04; boundary: B2/B5/B7; assets: AS-02, AS-05, AS-09.
- Impact: changed timestamps revive expired grants, extend jobs or qualification,
  make old results appear fresh or hide a network partition's unknown state.
- Required controls: TC-15, TC-18, TC-22, TC-23.
- Carrier: contract; existing envelope/grant time policy; runner hosts own budgets.
- Negative test `time-no-resurrection`: clock rollback/forward, stale authority
  samples and rebooted monotonic state cannot extend deadlines or revive revoked/
  expired authority; unknown state prevents reuse rather than becoming success.
- Positive control: a fresh authorized request runs within an externally enforced
  elapsed-time budget; stop/reconcile remains available during an admission hold.

### TM-DOS-01 — Payload exhausts host resources or obstructs stop

- Adversary: AD-01, AD-03; boundary: B3/B8; assets: AS-06, AS-02.
- Impact: process/memory/disk/I/O floods, log storms or orphaned descendants starve
  neighbours and supervision; payload exit is mistaken for execution absence.
- Required controls: TC-19, TC-20, TC-22.
- Carrier: runner hosts and backend choice; qualification tests enforced ceilings.
- Negative test `dos-host-bounds`: bounded fixture pressure hits each admitted
  ceiling with no unbounded spill; descendant escape attempts remain in the stop
  scope, and no reuse follows unconfirmed absence.
- Positive control: an ordinary within-budget job completes; stop/observe and
  recovery retain reserved resources during the pressure fixture.

### TM-DOS-02 — Submission and result floods exhaust the control plane

- Adversary: AD-02, AD-04, AD-05; boundary: B1/B2/B4/B5; assets: AS-02, AS-07.
- Impact: authentication, parsing, decompression, queue, store and retry floods
  consume shared capacity; noisy hosts deny unrelated admission or recovery.
- Required controls: TC-12, TC-16, TC-19, TC-20.
- Carrier: contract and admission/qualification; existing consumer service limits.
- Negative test `dos-control-budgets`: controlled oversized/deep/many-object intake
  and repeated submissions exhaust only their bounded budget, produce typed
  refusal and leave no partial result or unbounded retained debt.
- Positive control: another admitted principal's within-budget request and a
  protected stop/revoke operation remain serviceable under the fixture load.

### TM-AUTH-01 — Submitter lies about class or required capabilities

- Adversary: AD-02; boundary: B1/B2/B7; assets: AS-01, AS-06, AS-09.
- Impact: caller labels select a trusted account, weaker backend, unrestricted
  network, device or credential-bearing image; unsupported falls back silently.
- Required controls: TC-01, TC-08, TC-21, TC-23.
- Carrier: contract and admission/qualification; dispatcher and broker class rules.
- Negative test `auth-class-intersection`: a caller-selected trusted label,
  unsupported capability or missing/conflicting class evidence refuses before
  launch, with no fallback backend, egress widening or broker binding.
- Positive control: the recorded untrusted job runs only on an exact qualified
  tuple satisfying the complete policy intersection; slice-0 refusal stays intact.

### TM-AUTH-02 — Transport or signed messages are mistaken for authority

- Adversary: AD-02, AD-04, AD-05; boundary: B1/B2/B5; assets: AS-01, AS-02.
- Impact: a valid signature, host membership or quoted instruction authorizes a
  different action/recipient; recursive delegation widens privileges.
- Required controls: TC-09, TC-12, TC-18, TC-21.
- Carrier: existing envelope/grant specs and service consumers; contract maps them.
- Negative test `auth-concrete-action`: validly signed but wrong-audience,
  revoked, widened or wrong-target requests refuse; job/result text cannot mint
  a grant, select a verifier protocol or acquire station command authority.
- Positive control: a fresh correctly scoped action passes under the selected
  protected protocol and attenuated grant, independently of transport delivery.

### TM-LIFE-01 — Retry, cancellation or crash creates a second live execution

- Adversary: AD-01, AD-02, AD-04; boundary: B2/B3/B5/B8; assets: AS-03, AS-06.
- Impact: unknown start is retried, stale stop kills a newer job, backend restart
  bypasses admission or cleanup discards outputs before complete custody.
- Required controls: TC-04, TC-15, TC-19, TC-22.
- Carrier: contract and runner hosts; dispatcher lifecycle and result custody rules.
- Negative test `life-unknown-fences`: interrupt launch/stop/export and retry;
  no second physical launch, stale-generation action, premature reuse or partial
  delivery occurs; unknown remains explicit until reconciled.
- Positive control: an identical retry resolves its original execution; after
  confirmed absence and complete bounded output custody, the next job may start.

### TM-QUAL-01 — Host invents or borrows qualification evidence

- Adversary: AD-04; boundary: B7/B2; assets: AS-09, AS-05.
- Impact: self-signed reports, another host's results or happy-path-only checks
  qualify an unsafe host and claim properties never tested.
- Required controls: TC-17, TC-21, TC-23.
- Carrier: admission/qualification; contract defines minimum evidence semantics.
- Negative test `qual-independent-scope`: self-asserted pass, mismatched tuple,
  missing negative evidence and fabricated receipt presented as attestation all
  refuse qualification; a job cannot overwrite qualification records.
- Positive control: an independently authorized reviewed report with matching
  tuple and paired evidence permits only its stated class and restrictions.

### TM-QUAL-02 — Updates, revocation or restore leave stale admission active

- Adversary: AD-04, AD-05; boundary: B7/B2; assets: AS-09, AS-06.
- Impact: a changed kernel, backend, image, boot/configuration or restored ledger
  continues on old evidence; unavailable revocation is treated as healthy.
- Required controls: TC-13, TC-14, TC-18, TC-22, TC-23.
- Carrier: admission/qualification and runner hosts; contract owns invalidation.
- Negative test `qual-drift-fence`: independently vary each tuple component and
  revoke/expire evidence or restore an older record; new launch refuses without
  losing stop/reconcile access, and the host cannot clear an external fence.
- Positive control: current unchanged qualified state permits its allowed job;
  reviewed requalification authorizes only the newly named tuple.

### TM-OPS-01 — Platform administration violates job confidentiality

- Adversary: AD-06, AD-04; boundary: B4/B8 and administration; assets: AS-03, AS-04, AS-08.
- Impact: operator access, crash capture, backups or excessive retention exposes
  job data; authors incorrectly assume confidentiality from the host owner.
- Required controls: TC-01, TC-08, TC-24.
- Carrier: contract and runner hosts; store authorization/retention policy.
- Negative test `ops-retention-access`: unauthorized retrieval and retrieval after
  the declared deletion boundary refuse, including backups and retained logs;
  secret-bearing initial verification requests refuse.
- Positive control: the current authorized requester retrieves retained output
  during the declared window; documented authorized administration is audited.

## Trusted computing base and exclusions

For local containment, the TCB includes hardware and firmware, boot chain, host
kernel, backend sandbox or VMM, exposed devices and their isolation, supervisor,
image verifier/loader, runner image's privileged setup, storage cleanup and the
network enforcement outside payload control. A guest kernel, where present,
may be compromised; the backend evaluation must identify which remaining layers
still enforce the boundary. No product name alone identifies an adequate TCB.

For platform protection against a compromised host, trust lies outside that host:
segmentation and upstream egress enforcement, bounded control/result gateways,
artifact-store authorization, admission and qualification records, protected
policy, platform signing/verification and key custody, revocation and time policy.
A host-reported firewall, reset or receipt cannot independently establish these
properties once the host is compromised. Scope any proposed pool reporting key
separately from platform signing authority; its compromise proves no platform act.

A job author must trust the platform and its administrators with submitted bytes,
execution memory, output and retention. No confidential-computing or privacy
against the operator guarantee is made. Initial verification admits no secrets;
input classification cannot reliably discover every hidden sensitive value.

Qualification names the exact OS/kernel, architecture, hardware/firmware class,
backend/version, image and profile digests, launcher/collector, identity mapping,
network policy and node configuration. A composed VM plus inner sandbox is a
separate tuple, including both enforcement layers. Missing capabilities refuse;
a weaker backend never silently inherits a stronger backend's claims.

Signed receipts prove only which admitted reporting key signed which bytes,
subject to that key's trust and revocation. They are not hardware attestation,
proof of honest execution, proof of cleanup or independent qualification.
Hardware attestation, if later proposed, needs its own verifier, threat model,
freshness and measurement-to-property argument; it is not enabled here.

Physical attacks, malicious manufacture and invasive hardware extraction are
outside the initial service guarantee. Advanced hardware/microarchitectural side
channels are not claimed eliminated. Pending Q-TM-03, do not admit workloads
whose required confidentiality depends on their elimination. A device without
qualified DMA/reset semantics stays unavailable rather than excluded silently.

Recorded runtime dependency identities do not establish semantic safety.
Semantic correctness of arbitrary outputs, malicious but valid dependencies,
undiscovered kernel/VMM defects and reliable service during upstream outages are
not established by this model. The platform still must limit blast radius,
report uncertainty, preserve recovery and refuse unsupported security promises.

## Residual risks and activation decisions

- Escape remains possible through unknown backend or hardware vulnerabilities.
  Dedicated hosts and external segmentation reduce impact; they do not prove
  containment of every future exploit.
- A compromised host can read and alter its jobs and lie about their execution.
  Independent checks may raise confidence only within their stated assumptions;
  repeated runs on identically compromised infrastructure are not independence.
- Shared-resource timing and traffic metadata may leak. Disallow incompatible
  placement until a stated sharing policy is approved; do not promise zero leakage.
- Allowed egress and results are information channels. Secret-free admission,
  minimization and scoped access reduce exposure; redaction cannot make hostile
  arbitrary outputs safe to execute or prove absence of hidden secrets.
- Host reset cannot be inferred from runner disposal. Suspected firmware or boot
  compromise needs externally authorized remediation or host retirement.
- Quotas bound damage but cannot promise uninterrupted service or prevent every
  Sybil campaign. Abuse thresholds and operator response remain necessary.
- Inert review still exposes people and models to deceptive content. Technical
  non-execution controls do not establish semantic immunity to prompt injection.

The following questions are activation gates for their affected capabilities.
Until resolved, admission refuses the unsupported property or workload; it does
not silently choose an answer. The same questions appear in the shared index.

| ID | Open decision and required carrier |
|---|---|
| Q-TM-01 | How do host enrollment, job delivery and result authentication work without placing any platform secrets or authority on a pool host? Contract must specify isolated pool credentials, gateway authority, key custody/revocation and compromise behavior. |
| Q-TM-02 | Which dependency destinations/protocols, DNS behavior, byte/connection limits and independently enforced upstream controls are admitted? Contract and runner hosts must publish the egress policy. |
| Q-TM-03 | Which concurrency classes, hardware restrictions and side-channel leakage are accepted? Contract and qualification must decide the initial sharing policy. |
| Q-TM-04 | What proves disk/memory/device reset, and when do suspected boot/firmware compromise require rebuild or retirement? Runner hosts and qualification must publish recovery evidence. |
| Q-TM-05 | Which result claims require independent verification, by whom, and with what failure independence? Contract must distinguish host-reported, independently checked and any future attested claims. |
| Q-TM-06 | What output/input/log/backup retention, deletion evidence, retrieval authorization and operator-access disclosure apply? Contract and store/runner-host rules must publish the complete lifecycle. |
| Q-TM-07 | Who approves and revokes qualification, how fresh is evidence, and how are active jobs handled on revocation or drift? Admission/qualification must publish authority and fencing. |
| Q-TM-08 | What numerical admission/runtime/transfer/storage budgets and result-format limits preserve recovery capacity, and which consumer owns each limit? Contract and runner hosts must bind them to admitted policy. |

## Hand-off: controls to specification carriers

A threat is mitigated only when every cited control is implemented and qualified
within its stated scope. The table assigns specification responsibility; it does
not assert closure of unknown exploits, host dishonesty or accepted side channels.
Existing specs retain their semantics; new mappings require reviewed versioned
adapters, never extra fields silently added to closed envelopes.

| Control | Primary document that must specify it | Threats it must address |
|---|---|---|
| TC-01 | Runner hosts; contract fixes the no-platform-secrets invariant | TM-ISO-01, TM-ISO-02, TM-CRED-01, TM-CRED-03, TM-NET-01, TM-SUP-02, TM-AUTH-01, TM-OPS-01 |
| TC-02 | Backend choice; contract capability minimums | TM-ISO-01, TM-DATA-03, TM-DATA-05, TM-SUP-01 |
| TC-03 | Runner hosts; existing registrar/keymaker module access boundaries | TM-ISO-02, TM-CRED-01, TM-CRED-03, TM-NET-02 |
| TC-04 | Runner hosts; contract teardown/retention semantics | TM-DATA-01, TM-DATA-03, TM-CRED-02, TM-LIFE-01 |
| TC-05 | Contract; runner hosts and artifact/input store | TM-DATA-02, TM-DATA-04, TM-SUP-01 |
| TC-06 | Backend choice; admission/qualification | TM-DATA-03, TM-SIDE-01 |
| TC-07 | Contract; admission/qualification | TM-SIDE-01 |
| TC-08 | Contract; existing broker R-UNTRUSTED and dispatcher R-VERIFY | TM-CRED-01, TM-CRED-02, TM-AUTH-01, TM-OPS-01 |
| TC-09 | Contract; existing registrar and keymaker module specifications | TM-ISO-02, TM-CRED-01, TM-CRED-03, TM-AUTH-02 |
| TC-10 | Runner hosts; contract egress requirements | TM-NET-01, TM-NET-02, TM-NET-03, TM-CRED-02 |
| TC-11 | Runner hosts; contract DNS/destination requirements | TM-ISO-02, TM-NET-02 |
| TC-12 | Contract; existing messaging/envelope and store consumers | TM-DATA-02, TM-DATA-04, TM-NET-02, TM-NET-03, TM-RES-01, TM-DOS-02, TM-AUTH-02, TM-DATA-05 |
| TC-13 | Runner hosts; backend choice and admission/qualification | TM-ISO-01, TM-SUP-01, TM-SUP-02, TM-QUAL-02, TM-DATA-05 |
| TC-14 | Runner hosts; admission/qualification | TM-ISO-01, TM-SUP-02, TM-QUAL-02 |
| TC-15 | Contract; result intake and artifact-store consumers | TM-DATA-04, TM-NET-03, TM-RES-01, TM-RES-03, TM-TIME-01, TM-LIFE-01 |
| TC-16 | Contract; runner-host telemetry/collector services and existing dispatcher R-TRANSFER/result consumer | TM-RES-02, TM-DOS-02, TM-DATA-05 |
| TC-17 | Contract; admission/qualification | TM-RES-03, TM-QUAL-01 |
| TC-18 | Contract; existing envelope/grant time policy and runner-host budgets | TM-RES-01, TM-TIME-01, TM-AUTH-02, TM-QUAL-02 |
| TC-19 | Runner hosts; backend choice | TM-NET-01, TM-RES-02, TM-DOS-01, TM-DOS-02, TM-LIFE-01, TM-DATA-05 |
| TC-20 | Contract; admission and result consumer limits | TM-NET-01, TM-RES-02, TM-DOS-01, TM-DOS-02, TM-DATA-05 |
| TC-21 | Contract; admission/qualification and existing dispatcher/envelope/grant consumers | TM-DATA-02, TM-DATA-04, TM-SIDE-01, TM-CRED-03, TM-NET-03, TM-RES-02, TM-AUTH-01, TM-AUTH-02, TM-QUAL-01 |
| TC-22 | Contract; runner hosts and existing dispatcher lifecycle | TM-DATA-01, TM-RES-01, TM-TIME-01, TM-DOS-01, TM-LIFE-01, TM-QUAL-02, TM-DATA-05 |
| TC-23 | Admission/qualification; backend choice defines measured scope | TM-DATA-03, TM-SIDE-01, TM-SUP-01, TM-SUP-02, TM-RES-03, TM-TIME-01, TM-AUTH-01, TM-QUAL-01, TM-QUAL-02 |
| TC-24 | Contract; runner hosts and artifact/input-store retention | TM-DATA-01, TM-DATA-04, TM-CRED-02, TM-OPS-01 |
| TC-25 | Contract interpretation inventory; runner-host preparation/transfer/collector services; admission/qualification | TM-DATA-05 |

## Public reference baseline

These are inspected public specification snapshots, not deployment evidence.
No external isolation-product behavior is asserted from their contents.

- [Dispatcher specification](https://github.com/relux-works/swarma-dispatcher/blob/b719e2307f5f4d6284abbd1ba60c42330f2d6ae8/spec/dispatcher.md):
  §3.4 R-NETBIND/R-NET-TOOL/R-EXEC, §3.6 R-UNTRUSTED and review admission,
  §5.2 transfer and inert candidate review, §5.3 R-VERIFY, R-SHARED host rules.
- [Agent stations, CIP-0024](https://github.com/relux-works/curator-spec/blob/d6c40195962d9fa3ccafa2c2001c2312c6c5e086/cips/CIP-0024-agent-stations.md):
  station identity, protected controls, custody and authority are a separate track.
- [Credential broker specification](https://github.com/relux-works/swarma-credential-broker/blob/f2c01f52477b5885aefd5b6bd04d09a6a22813b2/spec/broker.md):
  R-UNTRUSTED forbids bindings and credential leases; missing class evidence refuses.
- [Registrar specification](https://github.com/relux-works/swarma-registrar/blob/2219f70358fb7b5c7d66f229c80a8ba4a1fe0230/spec/registrar.md):
  enrollment and helper identity boundaries; claims are not kernel peer identity.
- [Key custody specification](https://github.com/relux-works/swarma-keymaker/blob/46825e7c12cc0d5733ba057bb1f9609caf2925a4/spec/keymaker.md):
  §3.1 kernel peer UID and active generation; signatures do not prove correctness.
- [Platform envelopes and grants](https://github.com/relux-works/waggle/blob/67df75bec015ea463f685e4daf4975ecda18816a/spec/swarm-envelope-2.md):
  protected protocol selection, concrete action authority, attenuation, time,
  revocation and replay requirements; proposed interfaces are not activated here.
