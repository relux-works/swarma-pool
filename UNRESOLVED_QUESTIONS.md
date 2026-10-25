# Unresolved questions

Status: Draft (2026-10-25).

The platform owners retain all decisions below. Every question remains open;
recommendations are not approvals. Published companion contracts are linked from
the [repository overview](README.md).

- Q-TM-01: Specify pool enrollment, delivery and result authentication without platform secrets or authority on pool hosts; isolate pool credentials and gateway authority ([research/threat-model.md](research/threat-model.md)).
- Q-TM-02: Decide dependency egress, DNS, destinations/protocols, traffic limits and independent upstream enforcement ([research/threat-model.md](research/threat-model.md)).
- Q-TM-03: Decide concurrent sharing classes, hardware restrictions and accepted side-channel leakage ([research/threat-model.md](research/threat-model.md)).
- Q-TM-04: Specify storage/memory/device reset evidence and rebuild/retirement rules for boot or firmware compromise ([research/threat-model.md](research/threat-model.md)).
- Q-TM-05: Decide result evidence levels and independent verification authority and failure independence ([research/threat-model.md](research/threat-model.md)).
- Q-TM-06: Specify input/output/log/backup retention, deletion evidence, retrieval authorization and disclosed operator access ([research/threat-model.md](research/threat-model.md)).
- Q-TM-07: Decide qualification approval/revocation authority, freshness, drift and disposition of active jobs ([research/threat-model.md](research/threat-model.md)).
- Q-TM-08: Publish admission, runtime, transfer, storage and result-format limits with ownership and reserved recovery capacity ([research/threat-model.md](research/threat-model.md)).

## Isolation backend evaluation

- Which backend qualification and acceptance obligations discharge the accepted threat model, including compromised hosts, platform visibility, side channels and truthful result claims? See [research/isolation-backends.md](research/isolation-backends.md) and its AD-01–AD-06 mapping to [research/threat-model.md](research/threat-model.md).
- Who owns and validates launch configuration, images, runtime flags, annotations and templates? Job authors must not acquire host launch authority through these inputs.
- Is one job always one disposable sandbox/VM or Kata pod, and which concurrent or sequential sharing is permitted?
- Which physical/virtual host, CPU architecture, kernel, controller and nested-virtualization combinations must be supported? Pin the complete runtime and dependency bill of materials.
- Where are egress, DNS/proxy, host/metadata/peer denial and authenticated artifact/result collection enforced outside guest authority?
- How will whole-job CPU, memory, writable bytes, IO/network, PIDs, helpers and logs be bounded, especially on macOS and in Kata overhead groups?
- How are images imported, immutable inputs protected, snapshots/clones reseeded, external volumes tracked and interrupted teardown reconciled? What is the required residue/erasure standard?
- Which macOS license and permitted public-service model applies to the deployed OS, including guest count, leasing/exclusivity conditions and any written agreement? Is Tart's applicable source-license service use permitted?
- Which builds/tests require Xcode/macOS APIs, debugger/ptrace, nested containers/KVM or GPUs, and which unsupported behaviors must fail admission without fallback?
- Which real test machines, SLOs and acceptance owners will validate U01–U25, resolve incomplete advisory inventories and compare cold/warm lifecycle cost before choosing a backend?

## Backend choice decisions

- D-01: Require hardware-backed Firecracker as the first Linux class, or authorize a separately qualified Sentry class? Recommended: hardware first, no automatic fallback. See [research/backend-choice.md](research/backend-choice.md).
- D-02: Permit concurrent mutually untrusted jobs or require exclusive physical-host allocation including parent layers? Recommended: one live job per physical host initially, with residual side channels disclosed. See [research/backend-choice.md](research/backend-choice.md).
- D-03: Offer Linux first, Linux-on-Mac, or macOS/Xcode guests? Recommended: Linux first; defer both Mac classes until separate technical and applicable service-license qualification. See [research/backend-choice.md](research/backend-choice.md).
- D-04: Qualify bare metal or composed/nested hosts first? Recommended: bare metal with pinned supported KVM/CPU/kernel; composed placement waits for verifiable parent topology and all-layer evidence. See [research/backend-choice.md](research/backend-choice.md).
- D-05: Accept bounded root jailer/supervisor setup, stop and cleanup plus a per-job non-root VMM, or require entirely root-free operation? Recommended: explicit minimal root duties; zero-root policy reopens the choice. See [research/backend-choice.md](research/backend-choice.md).
- D-06: Who supplies the disposable representative pool test host, independent observability, test owner and reviewer? Recommended: qualification owner supplies a dedicated Linux machine first; Apple hardware is a separate later allocation. See [research/backend-choice.md](research/backend-choice.md).
- D-07: What exact dependency/transfer egress and DNS/redirect policy is allowed? Recommended: independent upstream default-deny gateway, scoped approved inputs and bounded traffic; no platform routes. See [research/backend-choice.md](research/backend-choice.md).
- D-08: What exact residue-destruction and retention standard is required? Recommended: demonstrated successor-visible state removal/reset, secret-free non-sensitive workloads and authorized external retention; no physical secure-erasure claim. See [research/backend-choice.md](research/backend-choice.md).
- D-09: What hard whole-job budgets, original stop/cleanup/revoke deadlines and recovery reserves are approved? Recommended: measure the small fixed fixture and publish qualified caps/bounds before activation. See [research/backend-choice.md](research/backend-choice.md).
- D-10: Who confirms independent backend facts and authorizes exact-tuple activation after EIC/CIP prerequisites? Recommended: host-reported results only initially; no activation until independent fact check and complete qualification/review succeed. See [research/backend-choice.md](research/backend-choice.md).

## Pool qualification decisions

- Q-PQ-01: Name independent provisioning, qualification, enrollment and final serving authorities; require their scoped recorded approvals. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-02: Approve the pool class/catalogue extension and separate two-map matrix section, with compatible independent kit/report versions; retain station codecs unchanged. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-03: Choose export/pool-key custody, rotation and recovery; software self-report or separately qualified hardware binding, with own-host registration. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-04: Publish finite revocation refresh, skew, challenge freshness, withdrawal and protected rollback-frontier recovery policy. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-05: Allocate a disposable representative Linux test machine, bounded fixtures, independent observation and test/review owners. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-06: Approve measured whole-unit caps, all original lifecycle bounds/quiet periods and actually usable recovery reserve. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-07: Decide sanitized public evidence availability/audit retention and owner disclosure, retaining private complete evidence separately. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-08: Approve nine-class cleanup standards and independent rebuild/replacement authority after host/firmware/outer-layer compromise. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-09: Publish typed pool-only enrollment/gateway/currentness and independent default-deny network bindings with final-check ownership. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-10: Assign complete-content custody, bounded whole intake, covered-copy deletion, authorized discard and minimal audit expiry services. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-11: Approve independent matrix review and a bounded scoped revocation/expiry withdrawal publication path. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.
- Q-PQ-12: Assign independent backend/advisory fact checking, mapping reconciliation and exact-class companion/carrier rollout acceptance. See [spec/pool-qualification.md](spec/pool-qualification.md) §8.

## Runner hosts

- Q-RH-01: Assign dedicated pool operations, independent qualification acceptance and external audit ownership; recommend credentials separate from platform roles ([spec/runner-hosts.md](spec/runner-hosts.md)).
- Q-RH-02: Decide physical versus rented substrate and ownership/control requirements; recommend dedicated platform-owned physical machines initially ([spec/runner-hosts.md](spec/runner-hosts.md)).
- Q-RH-03: Define post-compromise recovery and reuse evidence; recommend trusted independent reprovision/enrollment/qualification or replacement ([spec/runner-hosts.md](spec/runner-hosts.md)).
- Q-RH-04: Publish log/artifact/backup retention, operator visibility and deletion evidence; recommend minimal bounded retention and separate metadata/content scopes ([spec/runner-hosts.md](spec/runner-hosts.md)).
- Q-RH-05: Select pool-only enrollment/revocation and gateway purpose/object authentication; recommend no transitive platform authority ([spec/runner-hosts.md](spec/runner-hosts.md)).
- Q-RH-06: Approve per-backend cleanup standards, original response/completion bounds, quiet periods and measured recovery reserve before serving ([spec/runner-hosts.md](spec/runner-hosts.md)).
- Q-RH-07: Approve the separately chosen per-job backend, devices and sharing class; recommend one fresh unit per job and devices denied initially ([spec/runner-hosts.md](spec/runner-hosts.md)).
- Q-RH-08: Define bounded emergency administration through audited pool-only gateway sessions; recommend no direct login or fence bypass ([spec/runner-hosts.md](spec/runner-hosts.md)).
