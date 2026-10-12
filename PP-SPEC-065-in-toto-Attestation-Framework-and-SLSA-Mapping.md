# PP-SPEC-065: Proof of Efficacy Mapping to the in-toto Attestation Framework and SLSA

| Field | Value |
|---|---|
| Status | DRAFT v0.1 |
| Author | Craig Ellrod, Nebulonium, Inc. (dba HACKERverse®) |
| Date | October 8, 2026 |
| License | CC BY 4.0 |
| Maps to | in-toto Attestation Framework specification v1.2 and SLSA specification v1.2 |
| Series | Proof Protocol Framework Mapping Specifications |

---

## 1. Purpose

This specification defines how Proof Protocol artifacts can be expressed as in-toto attestations, and how SLSA Provenance and SLSA Verification Summary Attestations (VSAs) relate to Proof Protocol proof records, ProofBundle™ packages, ProofStamp™ tokens, and ProofRegister™ records.

The in-toto Attestation Framework and SLSA remain authoritative for their own terminology, type URIs, field names, levels, and architecture. This document defines a **Proof Protocol mapping** and does not supersede or modify either upstream specification.

## 2. Scope

The in-toto Attestation Framework supplies a layered format (Envelope, Statement, Predicate, Bundle) for authenticated metadata about software artifacts. SLSA supplies supply-chain security levels and predicate types for build provenance and verification summaries. Proof Protocol supplies the independent evidence model that determines whether a selected control performed as claimed.

The versions mapped are:

- **in-toto Attestation Framework specification v1.2** (`spec/v1`, Statement type `https://in-toto.io/Statement/v1`, DSSE v1.0 as the recommended envelope), maintained by the in-toto project, a Cloud Native Computing Foundation (CNCF) graduated project; and
- **SLSA specification v1.2**, approved and released 24 November 2025, which adds the Source Track to the Build Track and is backwards compatible with v1.1. SLSA is part of the Open Source Security Foundation (OpenSSF). Mapped predicate types are SLSA Provenance `https://slsa.dev/provenance/v1` and SLSA VSA `https://slsa.dev/verification_summary/v1`.

Independent witnessing, proof validity, evidence capture, packaging, anchoring, and efficacy semantics are defined elsewhere in the Proof Protocol specification family.

## 3. Core Question

Proof of efficacy asks:

> **Was there a control, and did it work?**

in-toto and SLSA can authenticate and describe how an artifact was produced and whether it met a supply-chain policy. They do not determine whether a security control in that artifact worked under adversarial conditions. This mapping defines how a proof record, ProofBundle™, ProofStamp™, or ProofRegister™ entry can be expressed in, carried by, or referenced from in-toto and SLSA attestations without changing what constitutes proof.

A Proof Protocol result does **not** by itself establish legal, audit, regulatory, or standards conformance unless the applicable authority separately determines that the evidence is sufficient.

## 4. Metric Definitions

Against a defined corpus or test condition, each applicable empirical case is recorded as **blocked**, **detected**, **missed**, or **INVALID** where those verdicts are meaningful.

Relevant measurements can include containment rate, detection rate, miss rate, false-positive rate, robustness under bypass or failure conditions, version-level results, and **INVALID** when required evidence is incomplete or broken.

Not every upstream construct is empirical. Envelope, signature, receipt, credential, manifest, message, and registration constructs MUST NOT be converted into efficacy scores merely because they can carry or reference a proof record.

## 5. Evidence Produced

Mapped tests can produce:

- **Proof records** binding external context, test case, control, system/version, verdict, timestamp, and evidence references;
- **ProofStamp™** trusted timestamps bound to evidence/verdict objects;
- **ProofBundle™** packages containing proof records, metrics, corpus manifests, and environment/context;
- **ProofRegister™** records for issued proof artifacts; and
- corpus and environment manifests identifying the tested conditions.

## 6. Mapping to in-toto Attestation Framework and SLSA

Exact upstream construct names, type URIs, and identifiers SHOULD be taken from the authoritative version being mapped and recorded in the ProofBundle™ where licensing permits. Proof Protocol artifact and field names are those defined in PP-SPEC-001 and PP-SPEC-003. This specification does not redefine upstream constructs.

| in-toto / SLSA construct | Proof Protocol artifact/treatment | Notes |
|---|---|---|
| Statement (`_type` `https://in-toto.io/Statement/v1`) | Carrier for a Proof Protocol attestation about a subject. | The Statement binds the attestation to subjects and names the predicate type. |
| `subject` (ResourceDescriptor list; `digest` required, `name`, `uri`) | The ProofBundle™, identified by the SHA-256 digest of `packet.json` and by `verifier.root_hash`, and, where applicable, the tested product artifact by digest. | Subjects are matched by digest. The tested product and the proof artifact SHOULD be listed as distinct subjects or in distinct Statements. |
| `predicateType` | A Proof Protocol predicate type URI in a namespace controlled by the Proof Protocol publisher, versioned with the Proof Protocol specification it encodes. | This mapping does not claim that any Proof Protocol predicate is vetted or listed by the in-toto maintainers. |
| `predicate` | Carries or references the `packet.json` fields `verifier`, `proof_protocol.benchmark`, `proof_protocol.pes`, `proof_protocol.nist_pulse`, and `proof_protocol.witness`, plus the Proof Protocol verdicts. | MUST NOT omit **INVALID** status, the verifier verdict, or version context. |
| ResourceDescriptor (`digest`, `uri`, `mediaType`, `annotations`) | References to `evidence.jsonl`, `verifier.txt`, `nist-pulse.json`, `witness.json`, `proofregister.json`, and `proofstamp.json` by digest. | Evidence files MUST remain byte-identical to those consumed by the verifier. |
| Envelope (DSSE v1.0; `payloadType` `application/vnd.in-toto+json`; `signatures`, `keyid`) | Signature by the ProofBundle™ assembler, witness, or HACKERverse® over the Statement. | A DSSE signature authenticates the Statement. It does not replace verification of `evidence.jsonl` against `verifier.signer_key`. |
| Bundle (grouped attestations) | MAY group a Proof Protocol attestation with SLSA Provenance and SLSA VSAs for the same tested product. | Grouping does not merge their semantics. |
| SLSA Provenance (`https://slsa.dev/provenance/v1`): `buildDefinition`, `runDetails`, `builder.id` | Supply-chain context for the system/version under test, bound into the proof record version context by artifact digest. | Proof Protocol consumes build provenance as context. It does not emit SLSA Provenance for the tested product. |
| SLSA Build Track and Source Track levels | Recorded as supply-chain posture of the tested product. | A SLSA level is not an efficacy result and MUST NOT be reported as one. |
| SLSA VSA (`https://slsa.dev/verification_summary/v1`): `verifier.id`, `policy`, `inputAttestations`, `verificationResult`, `verifiedLevels` | A VSA MAY record that a downstream verifier checked a ProofBundle™ under a policy whose `policy.uri` identifies the Proof Protocol specification version applied. | `verificationResult` (`PASSED` or `FAILED`) is a SLSA verification result, not a Proof Protocol verdict. Proof Protocol verdicts MUST be carried in a Proof Protocol predicate, not inferred from a VSA. |
| `inputAttestations` | MAY list a Proof Protocol attestation as an input when a downstream policy consumes it. | Listing an input does not by itself establish that the proof is conformant. |
| `resourceUri` (VSA) and `uri` (ResourceDescriptor) | MAY reference `proofregister_uri` or `proofstamp_uri`. | The ProofRegister™ record remains the canonical Proof Protocol anchor. |

## 7. Interoperability Rules

1. The authoritative upstream source, edition/version, and applicable identifier SHOULD be recorded.
2. Upstream identifiers and terminology MUST NOT be silently redefined.
3. Proof Protocol verdicts are Proof Protocol results; they are not upstream certifications, audit opinions, legal determinations, or endorsements.
4. A control's presence, configuration, activation, detection, and efficacy are distinct facts.
5. A policy decision, alert, log entry, or control trigger does not by itself establish efficacy when the claim concerns a downstream protected outcome.
6. Target, application, tool, SIEM, vendor, service, resource, or equivalent evidence SHOULD complete the evidence round trip when required to establish the result.
7. Missing required evidence MUST yield **INVALID**, not PASS.
8. Material changes to the tested system, policy, control, environment, mapping, or corpus SHOULD trigger retesting where they can affect the result.
9. Format, envelope, transport, and registration properties MUST remain distinguishable from empirically tested efficacy assertions.
10. Expressing a Proof Protocol artifact in in-toto / SLSA MUST NOT drop the evidence references, verdict, version context, or **INVALID** semantics of the source artifact.
11. External-format validity under in-toto / SLSA, such as a valid signature, receipt, credential, manifest, or message, is not by itself Proof of Efficacy.
12. The native Proof Protocol artifact remains authoritative. Any expression of it in in-toto / SLSA SHOULD reference it by digest (for example `verifier.root_hash` or the SHA-256 digest of `packet.json`) and by Proof Record ID where one exists.
13. A SLSA level, a `PASSED` VSA, or a valid DSSE signature MUST NOT be presented as a ProofStamp™ or as Proof of Efficacy.

## 8. Framework-Agnostic Architecture

> **Threat, control, audit, regulatory, and interoperability frameworks are pluggable inputs to Proof Protocol. Proof Protocol is framework-agnostic.**

External frameworks can identify **what to test or examine**, or how a result is carried. Proof Protocol independently establishes **whether the control worked and what evidence proves that result**.

No external framework is required for Proof Protocol to operate. An implementation MAY use the in-toto Attestation Framework and SLSA, another recognized framework or standard, a proprietary threat model, several frameworks simultaneously, or no external framework when the test condition is otherwise sufficiently defined.

Adding, replacing, muting, or removing a framework mapping does not alter the Proof Protocol architecture, evidence model, Proof of Efficacy determination, ProofBundle™, ProofStamp™, ProofRegister™, or independent corroboration requirements.

A framework mapping therefore establishes **interoperability**, not architectural dependency.

## 9. Relationship to Proof Protocol

This mapping is part of the Proof Protocol specification family maintained by Nebulonium, Inc.

The relationship is intentionally asymmetric:

> **The in-toto Attestation Framework and SLSA supply authenticated attestation formats and supply-chain integrity levels. Proof Protocol supplies the evidence model for determining whether a selected control performed as claimed.**

No affiliation, endorsement, certification, or sponsorship by the in-toto project, the Cloud Native Computing Foundation, the SLSA project, the Open Source Security Foundation, or The Linux Foundation is implied.

## 10. Source Framework, Attribution, and License

The in-toto Attestation Framework is external work of the in-toto project under the Cloud Native Computing Foundation. The in-toto attestation repository (https://github.com/in-toto/attestation) is distributed under the **Apache License, Version 2.0**, with the copyright line "Copyright 2021 in-toto Developers" (https://github.com/in-toto/attestation/blob/main/LICENSE). No NOTICE file was found at the repository root when checked on 2026-10-08.

SLSA is external work of the SLSA project, part of the Open Source Security Foundation. The SLSA specification is published under the **Community Specification License 1.0** (https://github.com/slsa-framework/slsa/blob/main/LICENSE.md), and the slsa.dev footer reads "© 2026 The Linux Foundation, under the terms of the Community Specification License 1.0" (https://slsa.dev/). Section 1.2 of that license states that attribution "is not required for implementations of the Specification"; it is required in derivative works based on Working Group materials.

This Proof Protocol mapping is independently authored and Licensed under the Greater Good Commercial License 1.0 (see LICENSE.md); text CC BY 4.0 under its Part S. Original inventor: Craig Thomas Ellrod (ORCID 0009-0004-1058-7666).

Where upstream material is copyrighted, licensed with conditions, or access-controlled, contributors MUST NOT copy upstream normative text into this repository merely to make the mapping self-contained. Reference identifiers and independently authored descriptions should be used.

### Required attribution

**SLSA (Community Specification License 1.0).** Section 1.2 of the license requires, verbatim:

> As a condition, anyone exercising this copyright license must include attribution to the Working Group in any derivative work based on materials developed by the Working Group.  That attribution must include, at minimum, the material’s name, version number, and source from where the materials were retrieved.  Attribution is not required for implementations of the Specification.

Source: https://github.com/slsa-framework/slsa/blob/main/LICENSE.md, Section 1.2. Although this mapping reproduces no SLSA text, it provides the following attribution in the form Section 1.2 requires:

> SLSA Specification v1.2, developed by the SLSA working group, part of the Open Source Security Foundation. Retrieved from https://slsa.dev/spec/v1.2/. © 2026 The Linux Foundation, under the terms of the Community Specification License 1.0.

Section 8.2 of the same license requires that "Any distributions of technical information to third parties must include a notice materially similar to the following". To the extent this mapping distributes SLSA technical information (type URIs and field names), that notice is reproduced verbatim:

> “THESE MATERIALS ARE PROVIDED “AS IS.” The Contributors and Licensees expressly disclaim any warranties (express, implied, or otherwise), including implied warranties of merchantability, non-infringement, fitness for a particular purpose, or title, related to the materials.  The entire risk as to implementing or otherwise using the materials is assumed by the implementer and user. IN NO EVENT WILL THE CONTRIBUTORS OR LICENSEES BE LIABLE TO ANY OTHER PARTY FOR LOST PROFITS OR ANY FORM OF INDIRECT, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES OF ANY CHARACTER FROM ANY CAUSES OF ACTION OF ANY KIND WITH RESPECT TO THIS DELIVERABLE OR ITS GOVERNING AGREEMENT, WHETHER BASED ON BREACH OF CONTRACT, TORT (INCLUDING NEGLIGENCE), OR OTHERWISE, AND WHETHER OR NOT THE OTHER MEMBER HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.”

This disclaimer applies to the referenced SLSA materials. It does not alter the CC BY 4.0 terms of original Proof Protocol material.

**in-toto (Apache License 2.0).** Apache-2.0 attaches notice obligations to redistribution of the Work or Derivative Works; this mapping redistributes neither. Recommended attribution line:

> in-toto Attestation Framework specification v1.2, https://github.com/in-toto/attestation. Copyright 2021 in-toto Developers. Licensed under the Apache License, Version 2.0. in-toto is a Cloud Native Computing Foundation project.

**What this specification reproduces:** type URIs, field and layer names, level names, and short factual statements of version and status. It does **not** reproduce in-toto or SLSA specification prose, schemas, protobuf definitions, or predicate definitions. No Apache-2.0 NOTICE propagation is triggered because no in-toto source or documentation is redistributed.

## 11. Versioning

This mapping is versioned independently of the in-toto Attestation Framework and SLSA. A new in-toto Statement major version, a new SLSA Provenance or VSA predicate major version, or a new SLSA specification version SHOULD trigger a mapping review and, where necessary, a new version identifying the upstream versions mapped.

---

*Proof Protocol · proofprotocol.io · CC BY 4.0*
