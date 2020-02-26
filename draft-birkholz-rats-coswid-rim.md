---
title: Reference Integrity Measurement Extension for Concise Software Identities
abbrev: CoSWID RIM
docname: draft-birkholz-rats-coswid-rim-latest
stand_alone: true
ipr: trust200902
area: Security
wg: SACM Working Group
kw: Internet-Draft
cat: std
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
  country: Germany
- ins: P. Uiterwijk
  name: Patrick Uiterwijk
  org: Red Hat
  email: puiterwijk@redhat.com
  street: 100 E Davie Street
  code: '27601'
  city: Raleigh
  country: Netherlands
- ins: J. Fitzgerald-McKay
  name: Jessica Fitzgerald-McKay
  org: Department of Defense
  email: jmfitz2@nsa.gov
  street: 9800 Savage Road
  city: Ft. Meade
  region: Maryland
  country: USA
- ins: M. Wiseman
  name: Monty Wiseman
  org: GE Global Research
  abbrev: GE Global Research
  email: monty.wiseman@ge.com
  street: ""
  code: ""
  city: ""
  region: ""
  country: USA
- ins: D. Waltermire
  name: David Waltermire
  org: National Institute of Standards and Technology
  abbrev: NIST
  email: david.waltermire@nist.gov
  street: 100 Bureau Drive
  city: Gaithersburg
  region: Maryland
  code: '20877'
  country: USA

normative:
  RFC2119:
  RFC8288:
  RFC8520:
  RFC7950:
  RFC8071:
  RFC8342:
  RFC8510:
  RFC8526:

informative:
  I-D.fedorkow-rats-network-device-attestation: riv
  I-D.ietf-rats-architecture: rats-arch
  I-D.ietf-rats-eat: eat

--- abstract

Concise Software Identification (CoSWID) tags identify and describe individual software components, patches, and installation bundles. CoSWID is based on ISO/IEC 19770-2:2015 2:2015 that provides a complementary XML schema definition (XSD) for Software Identification (SWID) tags. CoSWID supports the same features as the corresponding XML SWID tags. The CoSWID specification also includes more structured extensibility features and reduces a few of ambiguities that are not explicitly resolved in the ISO SXD. In this document, these extensibility features (extension points) are used to add attributes to the CoSWID specification. The new attributes allow for the use of CoSWID as Reference Integrity Measurements (RIM). There are three set of RIM features defined in this specification. 1.) attributes that support RIM manifests for Measured Boot, 2.) attributes that support the RPM package manager structure, and 3.) attributes that allow for OID to be used in the description of Reference Integrity Measurements.

--- middle

# Introduction

Reference Integrity Measurements describe the intended state of (composite) software components installed on a (composite) device. A measurement of all installed software components of a devices allows for assertions about the trustworthiness of the given device. In combination with a root of trust (RoT) for reporting (RTM), these measurements can be refined into evidence and enable IETF Remote Attestation Procedures (RATS). RATS support the decision process of whether to put trust in the trustworthiness of a device - or not.

The RATS architecture {{-rats-arch}} defines the roles Verifiers, Attesters, Endorsers, and Relying Parties. The RATS architecture also specifies that Attestation Evidence is created by Attesters and consumed by Verifiers. Ultimately, the goal is to enable a Relying Party to put trust in the trustworthiness of a remote peer (the Attester). Attestation Evidence is composed of believable assertions about an Attester's trustworthiness characteristics. In RATS, these assertions are called Claims. The Verifier conducts a set of appraisal procedures in order to assess the compliance of an Attester's trustworthiness characteristics.

The most prominent appraisal procedure in RATS is the comparison of Claim values included in Attestation Evidence with Reference Claim values provided by Endorsers (e.g. supply chain entities). The comparison of Claim values via Reference Claim values is vital for the assessment of compliance metrics with respect to software components installed on an Attester. A typical objective here is the remediation of already vulnerabilities discovered in certain versions of software components.

The Integrity Measurement Architecture (IMA) of the Linux Security Modules (LSM) provides a detailed Event Log (sometimes also referred to as a Measurement Log) that retains a sequence of hash measurements of every software sub-component (e.g. a firmware, an ELF executable, or a configuration file) that is created and appended to the sequence of measurements that composes the event log before the software component in question is started or read.

In essence, to enable this appraisal procedure conducted by Verfiers an Attester's IMA provides Event Logs that include the hash values of every started software component and therefore are part of the Attestation Evidence an Attester creates. The complementary well-known-values that Verifiers require are included in the Reference Integrity Measurements (RIM). RIMs can be provided via Software Identification tags created by Endorsers, such as the software creators, manufacturers, vendors, or other trusted third parties (e.g. supply chain or certification entities).

This document provides an extension to the CoSWID specification defined in RFC XXXX. The extension adds attributes to CoSWID tags that enable them to express RIMs. A vital subset of these attributes are illustrated in the TCG Reference Integrity Manifest Information Model [ref] specification. These attributes are added to the existing CoSWID specification via the most general extension point the CoSWID specification provides: $$coswid-extensions. An additional map definition named "reference-measurements" is added and is defined in section [ref] of this document.

Furthermore, a usage profile for signed CoSWID tags is defined that supports the software-component structure of the RPM packet management system [ref]. Signed CoSWID tags that are aligned with that software m

Reference Integrity Measurements provide a vital resource to be consumed by

## Requirements Notation

{::boilerplate bcp14}

# CoSWID Attribute Extensions

This specification defines three types of attribute sets that can be added to the CoSWID specification via its defined extension points.

1. Attributes that support RIM manifests for Measured Boot (often still referred to as Secure Boot),
2. Attributes that support the RPM package manager structure, and
3. Attributes that allow for OID to be used in the description of Reference Integrity Measurements.

## RIM requirements on existing CoSWID attributes

As defined by NIST IR 8060 [ref], there are required "Meta Attributes" for XML SWID tags that have to be included in a SWID tag in order to compose a valid SWID RIM. In this section, these attributes are mapped to CoSWID attributes and corresponding requirements on attributes defined in the CoSWID specification to compose valid NIST IR 8060 signed Payload content in the Concise Software Identity Reference Integrity Measurement (CoSWID RIM) representation.

The 'software-meta-entry' type defined in the CoSWID specification includes the optional members 'product', 'colloquial-version', 'revision', and 'edition'. These four members MUST be included in a CoSWID RIM in order to compose a valid Reference Integrity Measurement in alignment with NIST IR 8060. Furthermore, the semantics of the text (tstr) typed values MUST convey content that allows for semantic interoperability in a given scope (e.g. an administrative domain). The software-meta-entries are hints for the RATS verifier role to support discovery and matching of appropriate CoSWID RIM available to or discoverable by a verifier.

## RIM extensions for Measured Boot

The following attributes are derived from the TCG Reference Integrity Manifest Information Model [ref] specification. These attributes support the creation of very small CoSWID RIM tags that enable the Remote Integrity Verification (RIV {{-riv}}) of small things, i.e. constrained devices in constrained network environments. In consequence, the majority of the attributes listed in this section represent metadata about firmware and supply chain entities that provide firmware for a device (platform). Most identifiers defined in the table below are hints. This design is motivated by the vast variety of identifier types used in production today. It is vital to highlight that this variety poses a significant threshold that inhibits semantic self-descriptiveness. Nevertheless, interoperability beats self-descriptiveness. Nevertheless, a convergence towards a common identification scheme is highly encourages - alas not achieved yet - at the time of creating this proposed standard.

| Attribute Name | Quantity | Description
|---
| payload-type | 0-1 | 'direct': the representation used in this RIM (and referred RIMs) is using the CoSWID encoding as it's representation. 'indirect': the representation used in referred RIMs ('Support RIMs) is using a different representation than CoSWID as it's encoding. Analogously, a reference to the corresponding specification MUST be provided if the value is set to an equivalent of 'indirect' (see binding-spec-name and binding-spec-version). 'hybrid': the representation used in the referred RIMs ('Support RIMs') is a mix of CoSWID representations and other representations. In this case, a reference to the representation used MUST be included - even if it is the CoSWID representation - for every Support RIM (see 'binding-spec-name' and 'binding-spec-version' definition in this table).
| platform-configuration-uri-global | 0-1 | A byte-comparable reference to a Platform Configuration URI as defined by the TCG Platform Certificate Profile [ref TCG Platform Certificate Profile, Version 1.1 Revision 1 5 13 Feb 2019] for X.509v3 certificates that MUST be identical to the URI included in a TCG Platform Certificate pointing to a resource providing a copy of the CoSWID RIM this attribute is included in.
| platform-configuration-uri-local | 0-1 | A byte-comparable reference to a Platform Configuration URI defined by the TCG Platform Certificate Profile [ref TCG Platform Certificate Profile, Version 1.1 Revision 1 5 13 Feb 2019] that represents the resource at which a copy of this CoSWID RIM can be found within the (composite) device/platform itself.
| binding-spec-name | 1 | If the value of 'payload-type' is an equivalent to the enumeration 'indirect' the value of this attribute MUST contain a global unique text (tstr) identifier referring to the specification that defines the representation of the referred RIM in order to enable its decoding.
| binding-spec-version | 1 | If the value of 'payload-type' is an equivalent to the enumeration 'indirect' the value of this attribute MUST contain a unique version number with respect to the specification represented in the value of 'binding-spec-name'.
| platform-manufacturer-id | 0-1 | An identifier based on the IANA Private Enterprise Number registry that is assigned to firmware manufacturer. This identifier MUST be included unless the firmware manufacturer and the platform manufacturer are represented by the same text (tstr) value. Analogously, if the firmware manufacturer and the platform manufacturer are represented via the same text (tstr) value, this attribute MAY be omitted.
| platform-manufacturer-name | 0-1 | An identifier number (uint) value that uniquely represents the firmware manufacturer. This identifier MUST be included unless the firmware manufacturer and the platform platform manufacturer are represented via the same number (unit) value, this attribute MAY be omitted.
| platform-model-name | 1 | An identifier text (tstr) value enabling the identification of a certain device model/type composite. The reliability of this identifier is not absolute. In consequence this identifier MUST NOT be omitted. In an case, the use of this identifier requires foresight and preparation as it supports semantic interoperability, significantly.

~~~~ CDDL
<CODE BEGINS>
{::include coswid-rim-extension.cddl}
<CODE ENDS>
~~~~

--- back