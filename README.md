---
description: over view
---

# Uptick DID: An Introduction

Uptick DID uses the DID standard based on the [iden3](https://docs.iden3.io/) protocol.Uptick DID identity infrastructure facilitates trusted and secure relationships between apps and users, following the principles of self-sovereign identity and privacy by default. Uptick DID enables organizations on one side to issue verifiable credentials about users, and organizations on the other side to verify those claims via a suite of tools created for each member of the SSI ecosystem.

# Why Uptick DID?
Uptick DID, with the help of zero-knowledge proofs, lets users prove their identity without the need of exposing their private information. This ensures both the Freedom of Expression and Privacy by Default (User's identities are secured by zero-knowledge cryptography).

# Core Concepts of Uptick DID: Verifiable Credentials, Identity Holder, Issuer and Verifier (Triangle of Trust)

Every identity is identified by a unique identifier called DID (Decentralized Identifier). Every identity-based information is represented via a Verifiable Credentials (VCs). In the simplest terms, a VC represents any type of information related to an individual/enterprise/object. The VC could be as simple as the age of the entity or the highest degree held by it. It could be a membership certificate issued by a DAO, for instance.

The toolset made available by Uptick DID is fully compliant with the W3C standards. We have a definition spec. for the Uptick DID DID method.

The architecture of the framework is composed of three modules: Identity Holder, Issuer, and Verifier. These three, together, form what we call the Triangle of Trust. Let us see what role each entity plays in Uptick DID.

## 1)Identity Holder: 
An entity that holds claims in its Wallet. A VC, as mentioned above, is issued by an Issuer to the Holder. The Identity Holder generates zero-knowledge proofs of the VCs issued and presents these proofs to the Verifier, which verifies that the proof is authentic and matches specific criteria.

## 2)Issuer: 
An entity (person, organization, or thing) that issues VCs to the Holders. VCs are cryptographically signed by the Issuer. Every VC comes from an Issuer.

## 3)Verifier: 
A Verifier verifies the proof presented by a Holder. It requests the Holder to send a proof based on the VCs they hold in their wallet. While verifying a proof, the Verifier performs a set of checks, for example that the VC was signed by the expected Issuer and that the VC matches the criteria requested by the Verifier. The simplest example of a Verifier is a Bar that wants to verify if you are over 18. In the real world, the Identity Holder would need to provide an ID and show all their personal information. With Uptick DID, they only need to pass a proof.

A core concept here is the trust that must exist between a Verifier and an Issuer: the fact that the information contained inside a VC is cryptographically verifiable doesn't guarantee its truth. The Issuer must be a trusted and reputable party so that Verifier can consume the VCs originated by that Issuer.

