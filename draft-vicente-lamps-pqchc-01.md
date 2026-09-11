---
title: "PQC Hybrid Commitment (PQCHC) X.509 Extension"
abbrev: "PQCHC"
docname: draft-vicente-lamps-pqchc-01
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Security"
workgroup: "LAMPS Working Group"
keyword:
 - post-quantum cryptography
 - X.509
 - certificate extension
 - key commitment
 - hybrid cryptography
 - migration
venue:
  group: "LAMPS"
  type: "Working Group"
  mail: "spasm@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/spasm/"
  github: "sanctumsecops/draft-vicente-lamps-pqchc"
  latest: "https://sanctumsecops.github.io/draft-vicente-lamps-pqchc/draft-vicente-lamps-pqchc.html"

author:
 -
    ins: B. Vicente
    fullname: Brian Vicente
    organization: Sanctum SecOps LLC
    email: bvicente@sanctumsecops.com
    city: Pine City
    region: NY
    country: United States of America

normative:
  RFC2119:
  RFC5280:
  RFC8174:
  RFC9794:
  FIPS203:
    title: "Module-Lattice-Based Key-Encapsulation Mechanism Standard"
    author:
      org: "NIST"
    date: 2024-08
    target: https://doi.org/10.6028/NIST.FIPS.203
  FIPS204:
    title: "Module-Lattice-Based Digital Signature Standard"
    author:
      org: "NIST"
    date: 2024-08
    target: https://doi.org/10.6028/NIST.FIPS.204
  FIPS205:
    title: "Stateless Hash-Based Digital Signature Standard"
    author:
      org: "NIST"
    date: 2024-08
    target: https://doi.org/10.6028/NIST.FIPS.205

informative:
  RFC9773:
  RFC9763:
  NIST-IR-8547:
    title: "Transition to Post-Quantum Cryptography Standards"
    author:
      org: "NIST"
    date: 2024-11
    target: https://csrc.nist.gov/pubs/ir/8547/ipd
  I-D.ietf-lamps-pq-composite-sigs:
  I-D.ietf-lamps-pq-composite-kem:
  I-D.reddy-lamps-x509-pq-commit:
    title: "X.509 Certificate Extension for Post-Quantum Hosting Continuity"
    author:
      - ins: T. Reddy.K
        name: Tirumaleswar Reddy.K
        org: Nokia
      - ins: J. Gray
        name: John Gray
        org: Entrust
      - ins: Y. Sheffer
        name: Yaron Sheffer
        org: Intuit
    date: 2026-02-25
    target: https://datatracker.ietf.org/doc/draft-reddy-lamps-x509-pq-commit/
  I-D.ounsworth-lamps-pq-external-pubkeys:
    title: "External Public Key Information for use in PKI messages"
    author:
      - ins: M. Ounsworth
        name: Mike Ounsworth
        org: Entrust
      - ins: J. Gray
        name: John Gray
        org: Entrust
    date: 2024
    target: https://datatracker.ietf.org/doc/draft-ounsworth-lamps-pq-external-pubkeys/
  I-D.ietf-lamps-certdiscovery:
    title: "Certificate Discovery"
    author:
      - ins: T. Okubo
        name: Tomofumi Okubo
      - ins: C. Bonnell
        name: Corey Bonnell
      - ins: J. Gray
        name: John Gray
        org: Entrust
      - ins: M. Ounsworth
        name: Mike Ounsworth
        org: Entrust
    date: 2026-05
    target: https://datatracker.ietf.org/doc/draft-ietf-lamps-certdiscovery/
  CNSA2:
    title: "Commercial National Security Algorithm Suite 2.0"
    author:
      org: "NSA"
    date: 2022-09
    target: https://www.nsa.gov/Cybersecurity/Post-Quantum-Cybersecurity-Resources/
  FIPS180:
    title: "Secure Hash Standard (SHS)"
    author:
      org: "NIST"
    date: 2015-08
    target: https://doi.org/10.6028/NIST.FIPS.180-4
  FIPS202:
    title: "SHA-3 Standard: Permutation-Based Hash and Extendable-Output Functions"
    author:
      org: "NIST"
    date: 2015-08
    target: https://doi.org/10.6028/NIST.FIPS.202
  SHOR1994:
    title: "Algorithms for Quantum Computation: Discrete Logarithms and Factoring"
    author:
      - ins: P.W. Shor
        name: Peter W. Shor
    date: 1994
    seriesinfo:
      "Proceedings of the 35th Annual Symposium on Foundations of Computer Science": "pp. 124-134"
    target: https://doi.org/10.1109/SFCS.1994.365700
  GROVER1996:
    title: "A Fast Quantum Mechanical Algorithm for Database Search"
    author:
      - ins: L.K. Grover
        name: Lov K. Grover
    date: 1996
    seriesinfo:
      "Proceedings of the 28th Annual ACM Symposium on Theory of Computing": "pp. 212-219"
    target: https://doi.org/10.1145/237814.237866
  MOSCA2018:
    title: "Cybersecurity in an Era with Quantum Computers: Will We Be Ready?"
    author:
      - ins: M. Mosca
        name: Michele Mosca
    date: 2018
    seriesinfo:
      "IEEE Security & Privacy": "Vol. 16, No. 5, pp. 38-41"
    target: https://doi.org/10.1109/MSP.2018.3761723

--- abstract

The current Internet Public Key Infrastructure (PKI) lacks a
machine-verifiable, cryptographically bound mechanism for certificate
holders to commit, at issuance time, to the specific post-quantum (PQ)
or PQ/T hybrid key material they intend to present at the next
certificate renewal.  Without such a mechanism, relying parties cannot
distinguish a legitimate algorithm migration from a covert downgrade
attack — one in which a cryptographically relevant quantum computer
(CRQC) enables an adversary to suppress a PQ or composite certificate
at renewal and substitute one based solely on a classical algorithm
whose long-term security has been compromised.

This document defines the PQC Hybrid Commitment (PQCHC) X.509 v3
extension.  The extension carries an advisory, non-authoritative
commitment that is encoded as a cryptographic hash of the future
SubjectPublicKeyInfo the certificate subject intends to present in
the successor certificate, together with the intended committed
algorithm identifier and an expiry time for the commitment.  The
extension is non-critical, preserving backward compatibility with
legacy relying parties that do not recognize it.  PQCHC-aware relying
parties may use the commitment to detect downgrade at renewal time,
to integrate with ACME Renewal Information (ARI) scheduling, and to
provide an observable signal of an operator's concrete, binding
intent to complete PQ migration within a declared time window.


# Source and Archival {-}

Source for this draft is maintained at <https://github.com/Sanc-Admin/IETF-Final-Draft>.
A citable archival version of this document is available at Zenodo:
<https://doi.org/10.5281/zenodo.20584243>.
Author ORCID iD: <https://orcid.org/0009-0006-6395-5308>.

# IPR Considerations {-}

The author and Sanctum SecOps LLC hold or have applied for United States
patent applications covering subject matter related to this document.
Specifically, the subject matter herein overlaps in part with the disclosure
of U.S. Provisional Patent Application No. 64/080,137 ("Multi-Tenant Public
Key Infrastructure with Drift-Gated Issuance, Per-Transaction Semantic
Consistency Verification, and Topology-Aware Post-Quantum Certificate
Rotation", filed 2026-06-01) and U.S. Patent Application No. 19/698,870
("System and Method for Post-Quantum Cryptographic Readiness Observability
and Feedback in Networked Environments", attorney docket SANC-2026-002,
filed 2026-06-05).

By posting this Internet-Draft, the author submits to the IETF Trust the
rights described in Section 5 of BCP 78 and BCP 79. Patent licensing terms
are not yet known. Implementers and reviewers should consult the IETF
Datatracker IPR disclosure page for this document for current disclosure
status. No portion of this document is offered as a trade secret.

# Patent-Pending Claim Scope (Informational) {-}

The PQC Hybrid Commitment (PQCHC) extension specified in this document is
intentionally narrow and is offered as public prior art. It interoperates
with, but is distinct from, the following pending claim families identified
in the IPR Considerations note above. This summary is provided to inform
implementers of the protected scope; it is not an enabling disclosure and
does not waive any patent right. The specific algorithms, parameter values,
weights, thresholds, predicates, sequencing rules, and binding logic that
practice the pending claims are not disclosed in this Internet-Draft.

Commitment-Driven Drift-Gated Issuance: apparatus, method, and
non-transitory computer-readable medium claims (related to U.S. Provisional
Patent Application No. 64/080,137) directed to gating certificate issuance
and renewal on machine-verifiable cryptographic drift signals.

Topology-Aware Post-Quantum Rotation: apparatus, method, and
computer-readable medium claims (related to U.S. Provisional Patent
Application No. 64/080,137) directed to coordinating PQ certificate rotation
across multi-tenant deployments in a manner that accounts for
service-dependency topology.

Commitment-Linked Readiness Assessment: apparatus, method, and
computer-readable medium claims (related to U.S. Patent Application No.
19/698,870) directed to consuming commitment metadata as one input to a
quantum-readiness scoring and adequacy classification function.

Nothing in this Internet-Draft is to be construed as disclosing the manner
in which any of the above claims is reduced to practice.

# Author Credentials {-}

The author holds the following industry certifications, independently
verifiable through Pearson Credly at
<https://www.credly.com/users/brian-vicente>:

* CompTIA Security+ ce (2026-03-29 / valid through 2029-03-29)
* CompTIA Secure Infrastructure Specialist - CSIS Stackable (2026-03-29 / valid through 2029-03-29)
* CompTIA Secure Cloud Professional - CSCP Stackable (2026-03-29 / valid through 2027-06-29)
* CompTIA Cloud Admin Professional - CCAP Stackable (2025-10-10 / valid through 2027-06-29)
* CompTIA IT Operations Specialist - CIOS Stackable (2025-10-10 / valid through 2029-03-29)
* CompTIA Network+ ce (2025-10-10 / valid through 2029-03-29)
* CompTIA Cloud+ ce (2024-06-29 / valid through 2027-06-29)
* Linux Professional Institute Linux Essentials (2024-01-16)
* CompTIA A+ ce (2023-10-16 / valid through 2029-10-16)


--- middle

# Introduction

The standardization of post-quantum cryptographic (PQC) algorithms
by the National Institute of Standards and Technology (NIST) —
specifically ML-DSA {{FIPS204}}, ML-KEM {{FIPS203}}, and SLH-DSA
{{FIPS205}} — marks an inflection point for Internet PKI.  NIST IR
8547 {{NIST-IR-8547}} establishes that all quantum-vulnerable
traditional asymmetric algorithms are to be deprecated after 2030 and
disallowed for new protection after 2035.  The CNSA 2.0 suite
{{CNSA2}} requires National Security Systems to be fully compliant by
2031.  Operators of long-lived PKI infrastructure must therefore plan
and execute algorithm migrations across a multi-year window that
spans certificate lifetimes currently in service.

ML-DSA {{FIPS204}} and ML-KEM {{FIPS203}} are founded on the hardness of
Module Learning With Errors (MLWE) and Module Short Integer Solution (MSIS)
problems over polynomial rings — lattice-based problems for which no
efficient quantum algorithm is known.  SLH-DSA {{FIPS205}} is founded on
the collision resistance of cryptographic hash functions, providing
security even against adversaries with quantum computers running Grover's
algorithm {{GROVER1996}}, subject to the hash length requirements discussed
in Section 6.2.

The LAMPS Working Group has made substantial progress in defining how
PQ and PQ/T hybrid algorithms are encoded in X.509 certificates.
Composite ML-DSA {{I-D.ietf-lamps-pq-composite-sigs}} defines 18
composite signature algorithm identifiers; Composite ML-KEM
{{I-D.ietf-lamps-pq-composite-kem}} defines 12 composite KEM
identifiers.  RFC 9763 {{RFC9763}} allows a current traditional
certificate and a current PQ certificate to be bound to the same
subject.  The certificate discovery mechanism
{{I-D.ietf-lamps-certdiscovery}} enables a primary certificate to
point to a secondary certificate already issued.

Despite this progress, a specific lifecycle gap remains unaddressed:
no existing mechanism allows a certificate holder to make a
cryptographically verifiable commitment, in the current certificate,
to the specific PQ key material that will appear in the successor
certificate.  {{I-D.reddy-lamps-x509-pq-commit}} provides a
`continuityPeriod` field that declares, in days, the subject's
intent to continue presenting PQC or composite certificates after
the current certificate's notAfter.  This is a valuable declaration
of temporal intent, but it does not bind that intent to any specific
future key.  A relying party that caches this declaration cannot
determine whether the key in a future renewal certificate matches
what was actually intended at the time of issuance — nor can it
detect a key substitution by an adversary.

This document addresses that gap by defining a PQCHC extension that:

1. Carries a cryptographic hash of the future SubjectPublicKeyInfo
   (SPKI) in a FutureKeyCommitment structure, binding the commitment
   to specific key material.

2. Encodes the intended committed algorithm identifier alongside the
   hash, enabling relying parties to assess algorithm support without
   awaiting the successor certificate.

3. Declares a commitmentNotAfter time after which the commitment is
   considered expired and MUST NOT be enforced.

4. Provides an optional policyURI where operators may publish
   human-readable migration commitments and supporting documentation.

5. Remains non-critical per RFC 5280 {{RFC5280}} Section 4.2, so
   legacy relying parties that do not recognize the extension will
   ignore it, preserving backward compatibility throughout the
   migration window.

The PQCHC extension is advisory and non-authoritative.  The
extension does not modify RFC 5280 path validation semantics.
Enforcement of the commitment is left to PQCHC-aware application
software, certificate policy, and relying-party trust decisions.

## Relationship to Existing Work

The design of PQCHC is informed by several related efforts:

**draft-reddy-lamps-x509-pq-commit** {{I-D.reddy-lamps-x509-pq-commit}}:
The closest direct predecessor.  That document defines a `continuityPeriod`
integer expressing the number of days beyond notAfter during which the
subject intends to continue presenting PQC or composite certificates.
PQCHC addresses the explicit gap noted in that draft: the absence of
any cryptographic binding to a specific future key.

**draft-ounsworth-lamps-pq-external-pubkeys**
{{I-D.ounsworth-lamps-pq-external-pubkeys}}:
This expired individual submission references a current external public
key by hash.  PQCHC extends the same hash-of-SPKI principle into the
forward-commitment dimension: rather than referencing a key that exists
today, the PQCHC commitment references a key that is intended for use
in the future successor certificate.

**RFC 9763** {{RFC9763}}:
Binds a current traditional certificate and a current PQ certificate to
the same subject identity.  RFC 9763 addresses horizontal binding between
coexisting certificates; PQCHC addresses vertical, forward binding between
a current certificate and its future successor.

**draft-ietf-lamps-certdiscovery** {{I-D.ietf-lamps-certdiscovery}}:
Points to a secondary certificate via hash and URI.  The secondary
certificate must already exist.  PQCHC targets the prior period before
the successor certificate has been issued.

**Composite ML-DSA** {{I-D.ietf-lamps-pq-composite-sigs}} and
**Composite ML-KEM** {{I-D.ietf-lamps-pq-composite-kem}}:
Define how composite keys are encoded in current X.509 structures.  They
contain no commitment mechanism, no downgrade signaling, and no lifecycle
integration.  PQCHC is complementary: it can commit to either a pure
ML-DSA/ML-KEM key or a composite key as the intended successor.

**RFC 9773 (ACME ARI)** {{RFC9773}}:
Defines the ACME Renewal Information extension, including `renewalInfo`,
`suggestedWindow`, and the `replaces` field.  Section 5.4 of this document
describes how a PQCHC-bearing certificate can be integrated with ARI to
schedule commitment-aware renewals before the commitmentNotAfter deadline.

## Patent Notice

The authors are not aware of any patent or patent application that
covers the mechanisms described in this document.  This notice is
provided pursuant to IETF BCPs on intellectual property.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

The following terms are used throughout this document.  Terms
defined in RFC 9794 {{RFC9794}} ("PQ/T Hybrid terminology") are used
in accordance with the definitions therein.

**Committed Algorithm:**
The post-quantum or PQ/T hybrid algorithm that the certificate subject
declares it intends to use in the successor certificate, identified by
an AlgorithmIdentifier in the PQCHC extension.

**Committed Key Hash:**
A cryptographic hash computed over the DER encoding of the intended
future SubjectPublicKeyInfo, bound in the FutureKeyCommitment structure
of the PQCHC extension.  The hash serves as a pre-commitment to specific
future key material.

**Commitment Validity Time (commitmentNotAfter):**
The GeneralizedTime value in the PQCHC extension beyond which the
commitment is considered expired.  A PQCHC-aware relying party MUST NOT
enforce downgrade detection semantics after this time has passed.

**FutureKeyCommitment:**
The ASN.1 structure within the PQCHC extension that carries the hash
algorithm identifier, the Committed Key Hash, and an optional algorithm
hint for the future key.

**Issuance-Time Policy Binding:**
The property that the Committed Key Hash is embedded in the certificate
at issuance and signed by the issuing CA, making the commitment part of
the CA-signed certificate content.  The CA's signature over the PQCHC
extension binds the commitment to the certificate subject and the
issuance time.

**CRQC:**
A cryptographically relevant quantum computer — one with sufficient
qubit depth and fidelity to threaten the security of classical
asymmetric algorithms such as RSA, ECDSA, and ECDH.

**HNDL / TNFL:**
Harvest-Now-Decrypt-Later (HNDL) or Transcript-Now-Forge-Later (TNFL):
a threat model in which an adversary records classical-algorithm-protected
traffic today and decrypts or forges it once a CRQC becomes available.

**SPKI:**
SubjectPublicKeyInfo — the ASN.1 structure defined in RFC 5280 that
encodes a subject's public key and its associated algorithm identifier.

**Successor Certificate:**
The certificate issued to renew or replace the current certificate.
The PQCHC extension commits to the SPKI that is intended to appear
in the successor certificate.

# Problem Statement

## The Harvest-Now-Decrypt-Later and Transcript-Now-Forge-Later Threats

Adversaries with sufficient resources are believed to be collecting
encrypted data and signed records today under the expectation that a
CRQC will eventually be available to break the underlying classical
algorithms.  For long-lived secrets, key establishment material, and
non-repudiation records, this "harvest" phase has effectively already
begun.  NIST IR 8547 {{NIST-IR-8547}} projects a 2035 hard cutoff for
applying new cryptographic protection using quantum-vulnerable algorithms.

The specific threat to PKI arises from two quantum algorithms.  Shor's
algorithm {{SHOR1994}} solves the Integer Factorization Problem (IFP) and
the Elliptic Curve Discrete Logarithm Problem (ECDLP) in polynomial
quantum time, directly breaking the security of RSA (whose security rests
on IFP) and ECDSA/ECDH (whose security rests on ECDLP).  A certificate
whose public key uses RSA-2048, P-256, or any elliptic curve group of
equivalent classical security is rendered completely insecure by a
sufficiently capable CRQC running Shor's algorithm.  Grover's algorithm
{{GROVER1996}} provides a quadratic speedup for unstructured search,
effectively halving the bit-security of symmetric keys and hash functions;
this motivates the SHA-384/SHA-512 requirements in Section 4 and
Section 6.2 of this document.

The urgency of PQ migration is further quantified by Mosca's inequality
{{MOSCA2018}}: if the sum of (1) the time remaining until a CRQC is
available and (2) the secrecy lifetime required for data protected today
exceeds the time available to complete a migration, then migration should
be treated as overdue.  For long-lived PKI root certificates with
25-year validity periods, Mosca's inequality implies that migration cannot
be safely deferred even if a CRQC is not expected for a decade or more.
The PQCHC extension directly addresses the migration phase that Mosca's
inequality makes urgent: providing a machine-verifiable, cryptographically
bound mechanism for orderly per-certificate PQ transition.

The HNDL and TNFL threat models share a common implication for PKI: the
window between when a classical key is first used and when a CRQC breaks
it is the exposure window.  Narrowing this window requires early, orderly
migration to PQ algorithms — a process that spans multiple certificate
issuance cycles.

## The Multi-Year Migration Window

A typical TLS end-entity certificate has a validity period of 90 days to
two years.  A root CA certificate may have a validity period of 25 years.
The multi-year migration window created by NIST IR 8547 timelines means
that multiple successive certificate issuance events must each move
incrementally toward a fully PQ or PQ/T hybrid PKI.  During this window,
the same domain or service will present different certificates at
successive renewal events — some classical, some composite, and
eventually some fully PQ.

This creates the downgrade-at-renewal gap: an adversary who can present
a forged or substitute classical certificate at the moment of renewal,
preventing the PQ or composite certificate from being issued or accepted,
gains access to encrypted traffic for the lifetime of that substitute
certificate.

## The Downgrade-at-Renewal Gap

Downgrade attacks during the migration window take the following form:

1. The current certificate is about to expire or be renewed.
2. The legitimate operator has generated a PQ or composite successor key
   and intends to request issuance of a PQ or composite successor certificate.
3. An adversary, potentially equipped with a CRQC, intercepts or
   influences the renewal process — for example, by forging ACME order
   responses, by suppressing the PQ certificate in TLS negotiation, or by
   exploiting CA issuance policies that still accept classical CSRs.
4. The certificate presented after renewal is classical rather than PQ.
5. The relying party, lacking any machine-verifiable record of the prior
   commitment to a PQ key, accepts the classical certificate.

The adversary has achieved an undetectable downgrade.  The relying party
has no basis to reject the classical certificate because no prior
commitment was recorded in the previous certificate.

## Why Temporal Declarations Are Insufficient Without Cryptographic Binding

{{I-D.reddy-lamps-x509-pq-commit}} defines a `continuityPeriod` field
that declares the number of days beyond notAfter that the subject intends
to present PQC or composite certificates.  This declaration addresses the
observation-based heuristic: if a relying party has previously seen a
PQC certificate for a given subject, it may treat the absence of a PQC
certificate during the continuity window as suspicious.

However, temporal declarations without cryptographic binding have
the following limitations:

1. **No key specificity:** The declaration says "we will use some PQ
   algorithm" but does not commit to which key.  An adversary who
   generates a different PQ key pair and presents it at renewal bypasses
   the downgrade heuristic entirely — the relying party observes a PQ
   certificate and accepts it, not knowing a key substitution occurred.

2. **TOFU bootstrap problem:** A relying party that connects to a service
   for the first time during the migration window may never have seen a
   PQCHC-bearing certificate.  It has no cached commitment to use as a
   downgrade baseline.

3. **Silent cessation:** If the operator or an adversary presents a
   classical certificate without revoking the prior PQ certificate, the
   temporal declaration cannot distinguish intentional policy change from
   attack.  The draft acknowledges this as an open problem.

4. **No ACME-ARI integration:** The temporal declaration provides no
   hook for automated certificate management systems to schedule
   commitment-aware renewals that verify the PQ key material is consistent
   with what was committed at issuance time.

A cryptographically bound commitment to specific future key material
addresses each of these gaps.  Cryptographic binding means:

- The CA's signature over the commitment makes substitution of the
  committed hash a forgery that any verifier can detect.
- The hash of the future SPKI is specific to a particular key, not merely
  an algorithm family declaration.
- The commitmentNotAfter field provides a machine-processable schedule
  anchor that ACME-ARI can act on.

# Requirements for a Forward Key Commitment Extension

This section states requirements that a solution to the downgrade-at-renewal
gap SHOULD satisfy.  These requirements are stated in abstract,
non-implementation-specific language.

REQ-1: **Forward SPKI Hash Binding.**
A solution MUST provide a mechanism for embedding a cryptographic hash of
the future SubjectPublicKeyInfo in the current certificate, in a field that
is covered by the CA's signature.  The hash algorithm used MUST itself be
post-quantum resistant (i.e., resistant to the Grover speedup on second-preimage search, which
reduces SHA-256's effective quantum second-preimage resistance from 256
bits to approximately 128 bits, and SHA-384's from 384 bits to
approximately 192 bits).  SHA-384 or SHA-512, or their SHA-3 equivalents
of equivalent or greater length, SHOULD be used.

REQ-2: **Committed Algorithm Identification.**
A solution MUST include an AlgorithmIdentifier for the intended committed
algorithm, enabling relying parties to assess algorithm support without
waiting for the successor certificate to be issued.

REQ-3: **Machine-Verifiable Downgrade Detection.**
A PQCHC-aware relying party MUST be able to verify, at the time of
observing a successor certificate, whether the SPKI in that certificate
matches the Committed Key Hash in the predecessor certificate.  A mismatch
MUST be treated as a commitment failure.

REQ-4: **Commitment Validity Window.**
A solution MUST include a commitment expiry time (commitmentNotAfter)
expressed as a GeneralizedTime.  Relying parties MUST NOT enforce
commitment semantics after this time has passed.  The commitmentNotAfter
value SHOULD be set to a time no later than the latest date by which
the operator's declared algorithm migration policy requires completion.

REQ-5: **ACME-ARI Scheduling Hook.**
A solution SHOULD be integrable with ACME Renewal Information (ARI) {{RFC9773}}
so that a CA or ACME server can trigger a renewal with a `suggestedWindow`
that falls at or before commitmentNotAfter.  The renewal SHOULD result in
a successor certificate whose SPKI matches the Committed Key Hash.

REQ-6: **NIST Security Level Identification.**
The committed algorithm SHOULD be specified with sufficient precision to
allow relying parties to identify the NIST security level of the intended
future key.  For ML-DSA, this corresponds to the distinction between
ML-DSA-44, ML-DSA-65, and ML-DSA-87 as defined in {{FIPS204}}.

REQ-7: **Policy URI Binding.**
A solution SHOULD provide an optional URI field pointing to a human-readable
or machine-parseable policy document that describes the operator's
algorithm migration schedule and commitment governance.

REQ-8: **Non-Critical, Legacy-Safe Deployment.**
A solution MUST be deployable as a non-critical X.509 v3 extension per
RFC 5280 {{RFC5280}} Section 4.2.  Legacy relying parties that do not
recognize the extension MUST be unaffected — the extension MUST NOT cause
them to reject the certificate.  PQCHC-aware behavior is layered on top
of the existing RFC 5280 path validation semantics, not substituted for them.

REQ-9: **DER Encoding.**
The extension value MUST be encoded in Distinguished Encoding Rules (DER)
as specified in {{RFC5280}} Section 1.  All internal fields MUST use DER
canonical encoding.

REQ-10: **Experimental Deployability.**
Prior to formal IANA assignment of an id-pe OID, operators SHOULD be able
to use a private enterprise arc for experimental deployments, with
documentation clearly distinguishing the experimental arc from any future
permanent assignment.

# The PQCHC Extension

## Extension Overview

The PQC Hybrid Commitment (PQCHC) extension is an X.509 v3 extension
under the id-pe arc ({{RFC5280}} Section 4.2.2).  The extension MUST
be marked non-critical.

The extension carries an advisory, non-authoritative statement from
the certificate subject (as recorded by the issuing CA) that:

1. The subject intends the named committed algorithm to be present in
   the successor certificate issued before commitmentNotAfter.

2. The specific key material for that successor certificate is
   identified by a cryptographic hash over the future
   SubjectPublicKeyInfo, if the FutureKeyCommitment field is present.

3. A policy URI, if present, identifies further operator-published
   commitment governance documentation.

The extension content is covered by the issuing CA's signature over
the tbsCertificate, providing Issuance-Time Policy Binding (see
Section 3 definitions).

## ASN.1 Module

~~~ asn1
PQCHC-2026
  { iso(1) identified-organization(3) dod(6) internet(1)
    security(5) mechanisms(5) pkix(7) id-mod(0) TBD1 }

DEFINITIONS IMPLICIT TAGS ::= BEGIN

IMPORTS
    EXTENSION
        FROM PKIX-CommonTypes-2009
            { iso(1) identified-organization(3) dod(6) internet(1)
              security(5) mechanisms(5) pkix(7) id-mod(0) 57 }
    AlgorithmIdentifier{}
        FROM PKIX1Algorithms2008
            { iso(1) identified-organization(3) dod(6) internet(1)
              security(5) mechanisms(5) pkix(7) id-mod(0) 45 }
    id-pe
        FROM PKIX1Explicit-2009
            { iso(1) identified-organization(3) dod(6) internet(1)
              security(5) mechanisms(5) pkix(7) id-mod(0) 51 } ;

-- Object Identifier for the PQCHC Extension

id-pe-pqchc  OBJECT IDENTIFIER ::=  { id-pe TBD2 }

-- The PQCHC Extension

ext-PQCHC EXTENSION ::= {
    SYNTAX        PQCHCommitment
    IDENTIFIED BY id-pe-pqchc }

-- Main Extension Structure

PQCHCommitment ::= SEQUENCE {
    commitmentValid      BOOLEAN,
    committedAlgorithm   AlgorithmIdentifier { ALGORITHM, {...} },
    futureKeyCommitment  FutureKeyCommitment OPTIONAL,
    commitmentNotAfter   GeneralizedTime,
    policyURI            IA5String OPTIONAL
}

-- Future Key Commitment Structure

FutureKeyCommitment ::= SEQUENCE {
    hashAlgorithm        AlgorithmIdentifier { DIGEST-ALGORITHM, {...} },
    spkiHash             OCTET STRING,
    keyAlgorithmHint     AlgorithmIdentifier { ALGORITHM, {...} } OPTIONAL
}

END
~~~

## Field Definitions

### commitmentValid

The commitmentValid field is a BOOLEAN indicating whether the
commitment encoded in this extension is currently asserted as valid
by the certificate subject.

- A value of TRUE indicates the subject is asserting an active
  commitment.  This is the normative case.
- A value of FALSE indicates the commitment has been explicitly
  rescinded by the subject (e.g., due to a change in key generation
  schedule).  A PQCHC-aware relying party that observes commitmentValid
  set to FALSE in a certificate MUST treat any prior cached commitment
  for this subject as revoked.  Issuance of a certificate with
  commitmentValid FALSE is the mechanism by which subjects signal
  commitment revocation without revoking the certificate itself.

Conforming implementations MUST include this field.

### committedAlgorithm

The committedAlgorithm field is an AlgorithmIdentifier identifying
the post-quantum or PQ/T hybrid algorithm the subject intends to use
in the successor certificate.

The AlgorithmIdentifier MUST identify a post-quantum or composite
algorithm.  AlgorithmIdentifiers for ML-DSA variants are defined in
the LAMPS ML-DSA certificate profile (draft-ietf-lamps-dilithium-certificates).
AlgorithmIdentifiers for composite algorithms are defined in
{{I-D.ietf-lamps-pq-composite-sigs}} (for signatures) and
{{I-D.ietf-lamps-pq-composite-kem}} (for KEMs).

A PQCHC-aware relying party SHOULD verify that the algorithm OID in the
committedAlgorithm field is an algorithm the relying party supports, as
an early signal of future interoperability.

### futureKeyCommitment

The futureKeyCommitment field is OPTIONAL.  When present, it contains
a FutureKeyCommitment structure binding the commitment to a specific
public key value.  When absent, the commitment is algorithm-level only
(an advisory declaration of which algorithm will be used), without
binding to specific key material.

Implementations that wish to enable REQ-3 (machine-verifiable downgrade
detection at the key level) MUST include this field.  Its omission
reduces the strength of the commitment to algorithm-level intent only.

#### hashAlgorithm

The hashAlgorithm field in FutureKeyCommitment is an
AlgorithmIdentifier specifying the hash algorithm used to compute
spkiHash.  The hash is computed over the full DER encoding of the
future SubjectPublicKeyInfo.

The hash algorithm MUST provide at least 256 bits of preimage
resistance.  Implementations SHOULD use SHA-384 or SHA-512 ({{FIPS180}}),
or SHA3-384 or SHA3-512 ({{FIPS202}}).  Use of SHA-256 is permitted but
not recommended, as its effective quantum-collision resistance
is reduced to approximately 85 bits under the Grover algorithm.
Implementations MUST NOT use MD5, SHA-1, or SHA-224.

#### spkiHash

The spkiHash field is an OCTET STRING containing the output of
hashAlgorithm applied to the DER encoding of the full
SubjectPublicKeyInfo that is intended to appear in the successor
certificate.

~~~
spkiHash = Hash( hashAlgorithm, DER( future-SubjectPublicKeyInfo ) )
~~~

The DER encoding of SubjectPublicKeyInfo is taken over the complete
ASN.1 structure including the AlgorithmIdentifier and the subjectPublicKey
BIT STRING, as defined in RFC 5280 {{RFC5280}} Section 4.1.

#### keyAlgorithmHint

The keyAlgorithmHint field is OPTIONAL.  When present, it provides the
AlgorithmIdentifier for the algorithm used to generate the committed key,
as a convenience for relying parties that wish to assess future algorithm
compatibility before the successor certificate is issued.  In most cases,
keyAlgorithmHint and committedAlgorithm will carry the same OID, and
keyAlgorithmHint MAY be omitted.  keyAlgorithmHint is useful when
committedAlgorithm is a composite OID and the relying party needs to
independently assess each component's compatibility.

### commitmentNotAfter

The commitmentNotAfter field is a GeneralizedTime value specifying the
last time at which the commitment is asserted to be valid.  After this
time, a PQCHC-aware relying party MUST NOT rely on the commitment for
downgrade detection.

The commitmentNotAfter value:

- SHOULD NOT be later than the certificate's own notAfter field plus
  the maximum expected renewal lead time for the operator's environment.
  Excessively long commitmentNotAfter values prolong the exposure window
  if the committed key is later compromised.

- SHOULD be set so as to fall before or coincident with any applicable
  regulatory or compliance deadline (e.g., NIST IR 8547 2035 cutoff,
  or CNSA 2.0 2031 enforcement date {{CNSA2}}).

- MUST be expressed in the format "YYYYMMDDHHMMSSZ" per RFC 5280
  {{RFC5280}} Section 4.1.2.5.

### policyURI

The policyURI field is OPTIONAL.  When present, it is an IA5String
containing a URI that points to a human-readable or machine-parseable
document where the operator publishes its PQ algorithm migration policy,
key custody procedures, and commitment governance documentation.

Relying parties SHOULD NOT require the URI to be resolvable at certificate
validation time.  The URI is informational; its availability and content
are outside the scope of this specification.

## Processing Rules

### Certificate Issuance

1. The certificate subject SHALL generate the intended future PQ or
   composite key pair before requesting issuance of a certificate
   containing the PQCHC extension.

2. The subject computes spkiHash = Hash(hashAlgorithm, DER(future-SPKI))
   over the full DER encoding of the future SubjectPublicKeyInfo.

3. The subject includes the FutureKeyCommitment in the Certificate
   Signing Request (CSR) or in a separate signed commitment attestation
   submitted to the CA.

4. The issuing CA MUST verify that the commitmentValid field is TRUE
   before embedding an active commitment.

5. The issuing CA MUST verify that the committedAlgorithm OID refers to
   a post-quantum or composite algorithm.  A CA MUST NOT issue a
   certificate with a PQCHC extension in which committedAlgorithm
   identifies a classical-only algorithm.

6. The issuing CA SHOULD verify that commitmentNotAfter is not less than
   the current time plus a reasonable minimum commitment window (e.g.,
   at least as long as the certificate's own validity period).

7. The extension MUST be marked non-critical (criticality = FALSE).

8. The CA signs the tbsCertificate, which includes the extension, thereby
   providing Issuance-Time Policy Binding over the commitment.

### Certificate Consumption by PQCHC-Aware Relying Parties

1. Upon processing a certificate containing a PQCHC extension, a
   PQCHC-aware relying party:

   a. Verifies the certificate chain per RFC 5280 {{RFC5280}} path
      validation (Section 6) without modification.  The PQCHC extension
      does not alter path validation semantics.

   b. If commitmentValid is FALSE, clears any previously cached
      commitment for the subject and treats this certificate as
      having no active forward commitment.

   c. If commitmentValid is TRUE and the current time is before
      commitmentNotAfter, stores the following commitment record
      for the subject's identity (e.g., SubjectAltName):

      - The committed algorithm OID.
      - The FutureKeyCommitment (hashAlgorithm, spkiHash,
        keyAlgorithmHint), if present.
      - The commitmentNotAfter value.
      - The policyURI, if present.
      - The issuer and serial number of the current certificate
        (for correlation with ACME `replaces` tracking per {{RFC9773}}).

2. The commitment record MUST be purged when:

   a. The current time exceeds commitmentNotAfter.

   b. A successor certificate is accepted whose SPKI matches the
      Committed Key Hash (successful commitment fulfillment; see
      Section 5.3).

   c. A certificate with commitmentValid = FALSE is received for the
      subject.

   d. The relying party's local policy for commitment record retention
      has been exceeded (implementation-defined).

### Renewal and Commitment Fulfillment

1. When a PQCHC-aware relying party encounters a successor certificate
   for a subject for which a commitment record exists:

   a. Compute CandidateHash = Hash(hashAlgorithm from commitment record,
      DER(SPKI of successor certificate)).

   b. Compare CandidateHash to the spkiHash in the commitment record.

   c. If the hashes match: the commitment is FULFILLED.  The relying
      party SHOULD accept the successor certificate (subject to normal
      RFC 5280 path validation) and SHOULD log the commitment fulfillment
      event.  The commitment record is purged per Section 5.2(2b).

   d. If the hashes do NOT match: the commitment is VIOLATED.  The
      relying party MUST treat this as a commitment failure.  The
      appropriate response is implementation-defined and MAY include:

      - Rejecting the successor certificate for PQCHC-dependent
        application-layer operations.
      - Generating an alert or log entry indicating a commitment
        failure event.
      - Initiating out-of-band confirmation with the certificate
        subject via the policyURI.

      A relying party MUST NOT silently accept a successor certificate
      with a commitment violation when the FutureKeyCommitment field was
      present and commitmentValid was TRUE in the predecessor certificate.

2. If the FutureKeyCommitment field is absent in the predecessor
   certificate, the commitment is algorithm-level only.  In this case,
   a PQCHC-aware relying party SHOULD verify that the successor
   certificate's SPKI algorithm OID matches the committedAlgorithm OID.
   A mismatch between the committed algorithm and the successor
   certificate's key algorithm SHOULD be treated as a commitment
   failure.

### Downgrade Detection

1. If a PQCHC-aware relying party holds a commitment record for a
   subject with commitmentNotAfter in the future, and the relying
   party encounters a certificate for that subject whose SPKI is based
   solely on a classical algorithm (RSA, ECDSA, ECDH, EdDSA in
   standalone non-composite form):

   a. The relying party SHOULD treat this as a potential downgrade event.

   b. The relying party MUST NOT accept the certificate for PQCHC-dependent
      operations without additional out-of-band assurance.

   c. The relying party SHOULD log the downgrade event, including the
      subject identity, the committed algorithm OID, and the observed
      classical algorithm OID.

2. A commitment failure and a potential downgrade event are distinct:

   - A commitment failure occurs when the SPKI hash does not match the
     Committed Key Hash (key substitution case).
   - A downgrade event occurs when the algorithm family is classical
     rather than PQ or composite (algorithm regression case).

   Both are treated as protocol violations for PQCHC-dependent
   operations.

## Integration with ACME Renewal Information (ARI)

ACME Renewal Information {{RFC9773}} provides the `renewalInfo` resource,
`suggestedWindow`, and `replaces` field.  The following integration is
RECOMMENDED for operators using ACME certificate management:

1. **Scheduling:** When an ACME server manages certificates bearing
   the PQCHC extension, the `suggestedWindow` in the `renewalInfo`
   resource for that certificate SHOULD be set to begin no later than
   a reasonable lead time before commitmentNotAfter.  This ensures the
   renewal process completes and the successor certificate is issued
   while the commitment is still in force.

2. **Replacement tracking:** When an ACME order is submitted to renew
   a PQCHC-bearing certificate, the `replaces` field in the new ACME
   Order SHOULD reference the predecessor certificate (by the
   {issuer, serial} identifier as defined in {{RFC9773}} Section 5).
   This allows the CA and relying parties to correlate the renewal
   with the outstanding commitment.

3. **PQ key validation at order time:** A CA that receives an ACME Order
   with a `replaces` field referencing a PQCHC-bearing predecessor
   certificate SHOULD verify that the SPKI in the new Order's CSR
   matches the spkiHash in the predecessor's FutureKeyCommitment.  If
   the hash does not match, the CA SHOULD reject the Order with an
   appropriate error indicating a commitment mismatch.

   This step is OPTIONAL for CAs that do not implement PQCHC-aware
   order validation, and it requires the CA to have access to the
   predecessor certificate — which is typically possible via the
   issuer's own certificate repository or the `replaces` reference.

4. **Explanation URL:** The CA MAY set the `explanationURL` field of
   the `renewalInfo` resource to the same URI as the policyURI in the
   PQCHC extension, providing consistent reference between the ACME
   renewal workflow and the commitment governance documentation.

# Security Considerations

## Advisory, Not Authoritative

The PQCHC extension is advisory and non-authoritative.  The commitment
expressed in the extension is made by the certificate subject and
recorded by the CA; it is not a guarantee enforceable by technical
protocol alone.  A relying party's response to a commitment violation
is a matter of local policy.

A certificate subject that includes the PQCHC extension but fails to
honor the commitment (by presenting a classical or non-matching key at
renewal) incurs a protocol violation that PQCHC-aware relying parties
may detect and act upon.  The extension is analogous to a published
service commitment — machine-verifiable and CA-attested, but not
self-executing.

## Preimage Resistance and Hash Algorithm Selection

The security of the spkiHash commitment depends on the second-preimage
resistance of the hashAlgorithm.  An adversary who can find a second
preimage can substitute a different future key while maintaining the
hash match.

For a hash algorithm providing n bits of output, the relevant security
properties for the spkiHash commitment are:

- **Second-preimage resistance** (finding a different input with the same
  hash): classically 2^n operations; under Grover's algorithm applied to
  second-preimage search, approximately 2^(n/2) quantum operations.
- **Collision resistance** (finding any two inputs with the same hash):
  classically 2^(n/2) operations (birthday bound); under the
  Brassard-Høyer-Tapp (BHT) algorithm, approximately 2^(n/3) quantum
  operations.

For the spkiHash commitment, the relevant property is **second-preimage
resistance**: an adversary must find a distinct SubjectPublicKeyInfo that
hashes to the same value as the committed future SPKI.  The classical
security level equals the hash output length n; the quantum security level
under Grover is approximately n/2 bits.  This implies:

- SHA-256 (n=256): 256-bit classical second-preimage resistance;
  approximately 128-bit quantum second-preimage resistance.  SHA-256 SHOULD
  NOT be used as the sole hash algorithm in commitments extending beyond
  2030, as the effective quantum advantage is approaching practical
  relevance.
- SHA-384 (n=384): 384-bit classical second-preimage resistance;
  approximately 192-bit quantum second-preimage resistance.  SHA-384 is
  the RECOMMENDED minimum for high-assurance environments.
- SHA-512 (n=512) or SHA3-512 (n=512): 512-bit classical;
  approximately 256-bit quantum second-preimage resistance.  Preferred
  for commitments extending to 2035 or beyond.

The hash algorithm selection SHOULD be consistent with the security level
implied by the committedAlgorithm.  For example, a commitment to
ML-DSA-87 (NIST security category 5, targeting at least 256 bits of
classical security) warrants SHA-512 or SHA3-512.

## Public Key Correlation

The spkiHash field reveals information about the future public key to
any party that observes the current certificate.  Specifically:

- The hash provides a commitment binding: if two certificates appear
  with the same spkiHash and hashAlgorithm, they commit to the same
  future key.  This enables correlation of certificates across different
  organizational entities if the same future key hash appears in multiple
  PQCHC extensions.

- The hash does not reveal the future public key itself.  However, if the
  committed algorithm is an asymmetric algorithm with known key structure,
  an adversary with access to the public key at renewal time can trivially
  verify whether the key matches the prior commitment.

Operators SHOULD generate a unique future key pair for each PQCHC
commitment and MUST NOT reuse a committed future key in more than one
PQCHC extension unless the operator explicitly intends cross-certificate
correlation to be observable.

## Commitment Substitution Attack

An adversary who can perform a man-in-the-middle attack at the CA during
certificate issuance could, in principle, substitute a different spkiHash
for the value submitted by the legitimate subject.  The adversary would
then substitute the corresponding key at renewal time.

The Issuance-Time Policy Binding property (the CA's signature over the
extension in the tbsCertificate) prevents post-issuance substitution of
the hash.  However, it does not prevent issuance-time substitution if the
adversary can compromise the CA issuance pipeline.  Mitigations include:

- The subject retaining a signed copy of the CSR or commitment attestation
  containing the original spkiHash before submitting it to the CA.
- Certificate Transparency (CT) logging, which records the issued
  certificate and thus the committed spkiHash, enabling detection of
  discrepancies between submitted and issued commitments.
- Use of CSR attestation mechanisms such as draft-ietf-lamps-csr-attestation
  to provide hardware-backed proof of the private key controlling the
  committed SPKI.

## CRQC-Era Considerations

The PQCHC extension is designed for deployment during the transition to
PQC.  Once a CRQC becomes available:

- Existing classical certificates in the PKI can be forged or decrypted.
- CAs that sign certificates with classical algorithms can have their
  signing keys broken, enabling forgery of PQCHC extensions.

Therefore, the value of the PQCHC commitment depends on the certificate
chain and the CA's signing algorithm:

- A PQCHC extension in a certificate signed by a CA using only classical
  algorithms does not provide post-quantum security for the commitment
  itself.  A CRQC adversary could forge the CA signature and substitute
  the commitment.

- A PQCHC extension in a certificate signed by a CA using ML-DSA or a
  composite algorithm retains its commitment integrity even if a CRQC
  is available, because forging the CA signature would require breaking
  the PQ component.

Operators seeking CRQC-era integrity of their PQCHC commitments SHOULD
ensure their issuing CA chain uses ML-DSA or composite signing algorithms
before the commitment window extends into the period when a CRQC is
considered a realistic threat.  This is consistent with CNSA 2.0
{{CNSA2}} requirements, which mandate ML-DSA-87 for all NSS certificate
signatures by 2031.

## Downgrade-Forcing Attack

An adversary may attempt to force a downgrade by:

1. Preventing the legitimate subject from completing renewal during
   the ACME-ARI suggested window.
2. Presenting a classical or non-matching certificate after
   commitmentNotAfter has passed, when the relying party's downgrade
   detection has expired.

Mitigations:

- The operator SHOULD set commitmentNotAfter to a value sufficiently
  beyond the expected renewal date to account for operational delays,
  but not so far beyond as to create an extended window during which
  a compromised classical key is assumed to be valid.
- The CA SHOULD reject a renewal order for a PQCHC-bearing certificate
  that references a classical-only SPKI when ARI-integrated PQCHC
  validation is in force.
- Relying parties SHOULD log commitment expirations and flag any
  transitions from PQ or composite certificates to classical certificates
  that occur close to or after commitmentNotAfter.

## TOFU Bootstrap Problem

A relying party that encounters a PQCHC-bearing certificate for the
first time has no prior basis for validating the freshness of the
commitment.  The "trust on first use" (TOFU) problem means that if
the very first certificate observed for a subject already contains a
commitment violation, the relying party cannot detect it.

This problem is not specific to PQCHC; it is shared by all observation-
based downgrade detection mechanisms, including
{{I-D.reddy-lamps-x509-pq-commit}}.

Partial mitigations include:

- Certificate Transparency logs: relying parties can query CT logs to
  observe the history of certificates issued for a subject before the
  first direct connection.  A subject that has previously issued PQCHC-
  bearing certificates creates a CT-observable commitment history.
- ACME `replaces` field correlation: when ARI is in use, the chain of
  renewal Orders provides a verifiable history of certificate issuance
  events that can be cross-checked against CT log entries.
- Out-of-band pinning: high-assurance environments may publish the
  expected spkiHash via a separate, authenticated channel (e.g., DNSSEC-
  signed TXT records or operator-published PQCHC policy documents
  referenced by policyURI).

The TOFU bootstrap problem is acknowledged as a known limitation of this
specification.  Future work may address it through additional mechanisms
such as signed commitment announcements or integration with certificate
policy OIDs that attest to an operator's PQCHC enrollment.

# IANA Considerations

## id-pe OID Assignment

IANA is requested to assign an Object Identifier for the PQCHC extension
under the id-pe arc:

~~~
id-pe-pqchc  OBJECT IDENTIFIER ::=  { id-pe TBD2 }
~~~

The base id-pe arc is `1.3.6.1.5.5.7.1` as defined in RFC 5280
{{RFC5280}} Section 4.2.2.  The assigned id-pe value TBD2 will result
in a final OID of `1.3.6.1.5.5.7.1.TBD2`.

IANA is also requested to assign an Object Identifier for the ASN.1
module under the id-mod arc:

~~~
id-mod-pqchc-2026  OBJECT IDENTIFIER ::=  { id-mod TBD1 }
~~~

Both assignments are TBD pending IANA processing.

## Experimental OID Arc

Prior to permanent IANA assignment, operators wishing to conduct
experimental deployments of the PQCHC extension may use OIDs under
the IANA Private Enterprise Number (PEN) arc for Sanctum SecOps LLC:

~~~
1.3.6.1.4.1.65953
~~~

Specifically, the experimental arc for PQCHC extensions is:

~~~
id-pe-pqchc-experimental  OBJECT IDENTIFIER ::=
    { 1 3 6 1 4 1 65953 1 1 }
~~~

This experimental OID MUST NOT be used in production deployments or
in certificates submitted to public Certificate Transparency logs
without explicit documentation that the OID is experimental and subject
to change.  The experimental arc is distinct from any permanent
id-pe assignment.

# References

## Normative References

The following documents are referenced normatively in this specification.

RFC 2119 {{RFC2119}}: Key words for use in RFCs to Indicate Requirement Levels.

RFC 5280 {{RFC5280}}: Internet X.509 Public Key Infrastructure Certificate
and Certificate Revocation List (CRL) Profile.

RFC 8174 {{RFC8174}}: Ambiguity of Uppercase vs Lowercase in RFC 2119
Key Words.

RFC 9794 {{RFC9794}}: Terminology for Post-Quantum Traditional Hybrid Schemes.

FIPS 203 {{FIPS203}}: Module-Lattice-Based Key-Encapsulation Mechanism Standard.

FIPS 204 {{FIPS204}}: Module-Lattice-Based Digital Signature Standard.

FIPS 205 {{FIPS205}}: Stateless Hash-Based Digital Signature Standard.

## Informative References

RFC 9763 {{RFC9763}}: Related Certificates for Use in Multiple Authentications.

RFC 9773 {{RFC9773}}: ACME Renewal Information (ARI).

NIST IR 8547 {{NIST-IR-8547}}: Transition to Post-Quantum Cryptography Standards.

CNSA 2.0 {{CNSA2}}: NSA Commercial National Security Algorithm Suite 2.0.

draft-ietf-lamps-pq-composite-sigs {{I-D.ietf-lamps-pq-composite-sigs}}:
Composite ML-DSA For Use In Internet PKI.

draft-ietf-lamps-pq-composite-kem {{I-D.ietf-lamps-pq-composite-kem}}:
Composite ML-KEM For Use In Internet PKI.

draft-reddy-lamps-x509-pq-commit {{I-D.reddy-lamps-x509-pq-commit}}:
X.509 Certificate Extension for Post-Quantum Hosting Continuity (PQCHC).

draft-ounsworth-lamps-pq-external-pubkeys {{I-D.ounsworth-lamps-pq-external-pubkeys}}:
External Public Key Information for use in PKI messages.

draft-ietf-lamps-certdiscovery {{I-D.ietf-lamps-certdiscovery}}:
Certificate Discovery.

--- back

# Appendix A: Example PQCHC Extension DER Encoding

This appendix shows a non-normative, illustrative encoding of a PQCHC
extension.  The example commits to a future ML-DSA-65 key using a
SHA-384 hash.

## Certificate Subject Scenario

- Current certificate: ECDSA P-256, 2-year validity, expires 2027-01-01.
- Committed algorithm: ML-DSA-65 (OID 2.16.840.1.101.3.4.3.18).
- Future key: Pre-generated ML-DSA-65 key pair.  The DER encoding of the
  future SubjectPublicKeyInfo is hashed with SHA-384.
- commitmentNotAfter: 2027-04-01 (3 months beyond certificate notAfter to
  accommodate renewal lead time).

## ASN.1 Value (Informative)

~~~ asn1
PQCHCommitment ::= SEQUENCE {
    commitmentValid TRUE,
    committedAlgorithm SEQUENCE {
        algorithm  2.16.840.1.101.3.4.3.18  -- id-ML-DSA-65
    },
    futureKeyCommitment SEQUENCE {
        hashAlgorithm SEQUENCE {
            algorithm  2.16.840.1.101.3.4.2.2  -- id-sha384
        },
        spkiHash  OCTET STRING (48 bytes -- SHA-384 output),
        keyAlgorithmHint  ABSENT  -- same as committedAlgorithm
    },
    commitmentNotAfter  GeneralizedTime "20270401000000Z",
    policyURI  ABSENT
}
~~~

The extension appears in the certificate's extensions list as:

~~~ asn1
Extension ::= SEQUENCE {
    extnID    1.3.6.1.5.5.7.1.TBD2,  -- id-pe-pqchc
    critical  FALSE,                  -- MUST be non-critical
    extnValue OCTET STRING {          -- DER encoding of PQCHCommitment
        -- (DER bytes omitted; see description above)
    }
}
~~~

# Appendix B: Relationship to NIST IR 8547 Migration Timeline

NIST IR 8547 {{NIST-IR-8547}} (initial public draft, November 2024)
establishes the following transition timeline for quantum-vulnerable
algorithms:

| Milestone | Date       | Implication for PQCHC                            |
|-----------|------------|--------------------------------------------------|
| Deprecation of 112-bit classical algorithms | 2030 | PQCHC commitments for lower-security-level environments SHOULD have commitmentNotAfter no later than 2030. |
| Disallowment of all classical asymmetric algorithms | 2035 | PQCHC commitments in high-assurance environments SHOULD be set to complete migration before 2035. |
| CNSA 2.0 full enforcement for NSS | 2031 | PQCHC commitments in National Security System contexts SHOULD have commitmentNotAfter no later than 2031. |

The NIST IR 8547 note on hybrid modes states that the disallowment
of quantum-vulnerable algorithms after 2035 was not intended to apply
to hybrid modes that incorporate an approved PQC algorithm alongside a
quantum-vulnerable algorithm.  This means PQ/T composite certificates
(as defined in {{I-D.ietf-lamps-pq-composite-sigs}}) remain usable
beyond 2035 within the scope of that NIST guidance.  PQCHC commitments
to composite algorithms are therefore consistent with long-term
compliance, provided the composite includes an approved PQC component.

# Appendix C: Comparison with Related Mechanisms

| Mechanism | Forward Commitment? | Key-Specific Binding? | Downgrade Detection? | ARI Hook? | Non-Critical? |
|---|---|---|---|---|---|
| draft-reddy PQCHC (continuityPeriod) | Temporal only | No | Heuristic only | No | Yes |
| RFC 9763 RelatedCertificate | No (current binding) | Current cert only | No | No | Yes |
| draft-certdiscovery certHash | No (existing cert) | Existing cert hash | No | No | Yes |
| draft-ounsworth external-pubkeys | No (current key) | Current key hash | No | No | N/A |
| **This document (PQCHC -01)** | **Yes (future SPKI hash)** | **Yes (spkiHash)** | **Yes (REQ-3)** | **Yes (Section 5.4)** | **Yes** |

This comparison illustrates that the PQCHC extension as defined in
this document is the only mechanism in the current LAMPS WG landscape
that simultaneously provides forward key commitment with cryptographic
binding, machine-verifiable downgrade detection, and ACME-ARI
scheduling integration.

# Acknowledgments
{:numbered="false"}

The author thanks Tirumaleswar Reddy.K, John Gray, and Yaron Sheffer for
their work on draft-reddy-lamps-x509-pq-commit, which identified the
temporal commitment gap and provided the direct motivation for this work.

Thanks to Mike Ounsworth, Massimiliano Pala, Jan Klaußner, and Scott
Fluhrer for their work on composite ML-DSA and composite ML-KEM, which
establish the algorithm encoding infrastructure that PQCHC commitments
reference.

Thanks to Alison Becker, Rebecca Guthrie, and Michael J. Jenkins for
their work on RFC 9763 and the CNSA 2.0 PKIX profile, which clarify
the NSS certificate requirements that motivate PQCHC in high-assurance
contexts.

Thanks to the LAMPS Working Group for the broader PQC certificate
infrastructure this document builds upon.

This document was prepared for public review as a contribution to the
IETF LAMPS Working Group.  It describes a problem domain and
requirements; it does not disclose any proprietary internal
architecture.
