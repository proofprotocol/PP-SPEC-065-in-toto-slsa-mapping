# PP-SPEC-065: Proof of Efficacy Mapping to the in-toto Attestation Framework and SLSA

**Status:** DRAFT v0.1
**Author:** Craig Ellrod / Nebulonium, Inc. / HACKERverse®
**License:** Greater Good Commercial License 1.0 (GGL-C-1.0); text CC BY-ND 4.0 under Part S. See LICENSE.md.

**Normative specification:** [`PP-SPEC-065-in-toto-Attestation-Framework-and-SLSA-Mapping.md`](./PP-SPEC-065-in-toto-Attestation-Framework-and-SLSA-Mapping.md)

## Purpose

This repository defines a Proof Protocol mapping between in-toto Attestation Framework specification v1.2 and SLSA specification v1.2 and Proof Protocol evidence, efficacy, and proof semantics.

in-toto Attestation Framework and SLSA is treated as an interoperability standard that can express, carry, or register Proof Protocol artifacts. Proof Protocol remains framework-agnostic and independently establishes whether a selected control performed as claimed.

## Repository contents

- `PP-SPEC-065-in-toto-Attestation-Framework-and-SLSA-Mapping.md`: normative mapping specification
- `README.md`: repository overview
- `LICENSE`: license for original Proof Protocol material
- `CONTRIBUTING.md`: contribution guidance
- `CITATION.cff`: citation metadata

## Architectural principle

> **Threat, control, audit, regulatory, and interoperability frameworks are pluggable inputs to Proof Protocol. Proof Protocol is framework-agnostic.**

External frameworks and standards identify what may be tested or how results may be carried. Proof Protocol independently establishes whether a control worked and what evidence proves the result.

## Ownership and external-framework notice

The Proof Protocol mapping is independently authored. in-toto Attestation Framework and SLSA names, identifiers, trademarks, and upstream materials remain subject to their respective ownership and licensing. Mapping establishes interoperability, not dependency, endorsement, certification, or transfer of ownership. No affiliation, endorsement, certification, or sponsorship by the in-toto project, the Cloud Native Computing Foundation, the SLSA project, the Open Source Security Foundation, or The Linux Foundation is implied.

SLSA Specification v1.2, developed by the SLSA working group, part of the Open Source Security Foundation, retrieved from https://slsa.dev/spec/v1.2/, © 2026 The Linux Foundation, under the terms of the Community Specification License 1.0 (attribution per Section 1.2; the Section 8.2 distribution disclaimer is reproduced in Section 10 of the specification). in-toto Attestation Framework specification v1.2, Copyright 2021 in-toto Developers, licensed under the Apache License, Version 2.0; in-toto is a Cloud Native Computing Foundation project. No upstream specification text is reproduced.

Proof Protocol, ProofStamp, ProofBundle, and ProofRegister are trademarks of Nebulonium, Inc.
