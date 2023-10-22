# Issuer Overview

An Issuer is any subject that issues Verifiable Credentials. You can think of a credentials as a statement: something an Issuer says about another subject. For example, when a university (Issuer) says that a student (subject) has a degree, this is a credential.

An issuer might be:

- A DAO that issues “membership claims" to its members.
- A Government that issues ID to its citizens.
- A Face detection ML application that issues "proof of personhood" claims.
- An employer that endorses its employees.

Verifiable Credentials are a flexible data format able to express any type of information so that developers can unleash their creativity.


# Operating an issuer

There are some different ways one can perform issuer-related actions, that is, manage and issue credentials, establish connections with holders etc. These are the currently available options:

- Running an Issuer Node directly in your infrastructure.
- Utilizing the issuer node available in the Google Cloud Marketplace (soon available also on the AWS Marketplace).
- Adapting the [JS SDK](https://github.com/UptickNetwork/DID-js-sdk) to your application that issues credentials.
- Using SaaS vendors that leverage Uptick DID solutions.
- Making use of Uptick DID smart contracts for on-chain issuance.

# API Reference

## Identity

The identity endpoint is used to create and retrieve identities for a user/issuer. Uptick DID supports the creation of identities in the form of DIDs (Decentralized Identifiers).

Note: A DID is a cryptographically verifiable decentralized identifier. How a DID is verified is specified in its DID method. A DID resolves to a DID document that describes how to interact with the DID subject (The user that holds the identity). The DID document contains public keys that are used to authenticate the DID subject. An example of DID resolution could be a verifier resolving an Issuer's DID to get the public key, which is required to verify the signature on a Verifiable Credential.

A DID can be represented as:

```
did: did method: did method-specific identifier
```

This is a standard form of expressing a Decentralised Identifier as defined by the W3C DID Core 1.0 Specifications A user can have multiple DIDs.

## Create Identity

Function: Endpoint to create an Identifier for a user/issuer.

How it Works: The request to create an identity is sent to the Issuer Node. didMetaData is passed in the request body. This metadata is required to create Issuer's DID.

An example of a didMetaData passed in the request body of the Create Identity endpoint is shown below:


```
{
    "didMetadata":{
        "method": "iden3",
        "blockchain":"uptick",
        "network": "origin"
    }
}
```

[API Reference](http://54.179.233.10:3003/#post-/v1/identities)

## Get Identities

Function: Endpoint to retrieve all the identities (identifiers) of a user.

How it Works: The request to retrieve an identity is sent to the Issuer Node. didMetaData is passed in the request body.

The Issuer Node responds by sending a response message that contains:

- identifier: Identifier of the Issuer in the standard DID format. For example:
```
did:iden3:uptick:origin:2qNBWSAsyvaGBpqQVHk3E4cgChaN6ogaZnYCQUyoRQ
```

[API Reference](http://54.179.233.10:3003/#get-/v1/identities)


## Publish State On-Chain
Function: Endpoint to publish the Identity State on-chain.

How it Works: The identifier string in the DID format (retrieved from calling the Create Identity endpoint) is passed as a path variable in the request URL.

The Issuer Node responds by sending a response message that indicates the published state of the Identity. If there is no state to publish, the Node shows the message as: "no states to process". If the state is published, the Node sends the following information:

- claimsTreeRoot: The root of the Claims Merkle Tree. For example, d9c1e213584c4e7f444bdc42a5cdbd038556d1455a8344e3a1451c346461a41d

- revocationTreeRoot: The root of the Revocation Merkle Tree. For example, 0000000000000000000000000000000000000000000000000000000000000000

- rootOfRoots: The root of the Roots Merkle Tree. For example,9b952dab35ed362f62573c7b1bb0ee1e5dcb32109fafacb24c9b335fbddf4f2e

- state: The published state of the Identity. It is calculated by taking the roots of the Claims Tree, Revocation Tree, and Roots Tree. For example, a state could be: 1d9639360ad6e7fb86b8d2f55abd6201cae521e812496cd6606ce36a5829c316

- txID: Transaction ID of the Published state on-chain. For example, 0xaed59e4195e73ee6c4278be1a5e01ecbdf2ff9565511916200dedda9bb20ff38

[API Reference](http://54.179.233.10:3003/#post-/v1/-identifier-/state/publish)

## Claim

The collection of Claim endpoints is used to provide the following set of functionalities:

- Create a Verifiable Credential (VC)
- Retrieve a credential or a set of credentials
- Generate a JSON to create a QR code
- Update Identity State
- Revoke a Verifiable Credential
- Retrieve Revocation Status

A credential ID is assigned to a Verifiable Credential when it is created by an Issuer. A user can then retrieve a VC via its ID. If a credential is no longer valid or lost, it can be revoked (rendered inactive and cannot be used).

## Create Claim

Function: Endpoint to create a Verifiable Credential for a user.

How it Works: The DID (identifier string retrieved from calling the Create Identity endpoint) is passed as a path variable in the request URL. This is your DID identifier that you use as an issuer of credentials.

The following parameters are passed in the body of the request:

- credentialSchema: It is a template for a Verifiable Credential that guarantees the structure of a credential. This way, an Issuer, a Holder, and a Verifier can reference the data in a known way. Further details on the credentialSchema can be found in the Create Custom Schema section.
- type: the type of the credential schema sent.
- credentialSubject: Contains DID (Decentralized Identifier), i.e. did, of the user and the fields related to the data to be attested. You should have previously obtained this identifier from the user by performing a "basic auth" step, this can be done through the Authentication/QRcode step.
- expiration: Date of expiry of the Verifiable Credential.
  
Note: Depending on the schema a user opts for, the request body may contain some fields of the schema while leaving out the others. For example, in the API reference, we have considered the schema of the type KYCAgeCredential and therefore, included the birthday and documentType fields.

The Issuer Node responds by sending a response message that contains the string id, which is the ID of the Verifiable Credential created by the Issuer Node.

[API Reference](http://54.179.233.10:3003/#post-/v1/-identifier-/claims)

## Get Claim
Function: Endpoint to retrieve a Verifiable Credential based on its Claim ID (CID). This way, you can retrieve a credential issued by an Issuer based on this credential's ID.

How it Works: The DID (the identifier string retrieved from calling the Create Identity endpoint) and the Claim ID, i.e.id (or CID) of the Verifiable Credential (retrieved from calling the Create Claim endpoint) are passed as path variables in the request URL.

The endpoint requires to pass the issuer did and the Verifiable Credential CID as path variables in the request URL.

The server responds by sending the following data about the Verifiable Credential:

- Context: URL pointing to the JSON-LD Context of the Verifiable Credential.
- credentialSchema: URL pointing to the credential JSON schema.
- credentialStatus: Shows the URL to fetch the Revocation status of the credential, revocationNonce (zero or any value), type (type of Proof, for example, SparseMerkleTreeProof).
- credentialSubject: Contains details of the subject (to whom the credential is issued) and includes:

- - Credential Fields (for example, birthday and documentType in the case of KYCAgeCredential)
- - id: DID of the Subject
- - type: Type of credential for credentialSubject (for example, KYCAgeCredential)

- id: It is the id of the Verifiable Credential.
- expiration: The date on which the credential shall expire.
- issuer: DID of the Issuer.
- issuanceDate: The date on which the credential was issued by the Issuer.
proof: The proof that the user creates to prove that s/he is the real owner of the Verifiable Credential issued from the Issuer and that the Verifiable Credential that it holds is valid. It includes:

- - type of proof (for example, BJJSignature2021 or SparseMerkleTreeProof)
- - issuerData: It includes the Issuer's id (DID of the Issuer) and its state (value of its claimstreeroot, i.e. root of the claims (credential) tree)
- - authclaim: Value of authclaim along with its mtp existence (proof of its existence/non-existence in the Merkle tree)
- - coreclaim: Value of coreclaim along with signature (Issuer's signature which verifies that the credential is issued by a valid Issuer).
  
[API Reference](http://54.179.233.10:3003/#get-/v1/-identifier-/claims/-id-)

## Get Claims

Function: Endpoint to retrieve all the Verifiable Credentials issued by an Issuer.

How it Works: The DID (the identifier string retrieved from calling the Create Identity endpoint) is passed as path variables in the request URL.

You can retrieve a set of credentials based on different filters or criteria. These criteria can be added as the query-string parameters in the request URL. These filters (and their data types) are listed below:

- schemaType String: Type of schema. For example, schema based on Age-based KYC (KYCAgeCredential)
- schemaHash String: Hash of the schema. For example, c9b2370371b7fa8b3dab2a5ba81b6838
- subject String: Identifier of the Subject for which credentials are to be retrieved. For example, did:iden3:uptick:origin:2qE1BZ7gcmEoP2KppvFPCZqyzyb5tK9T6Gec5HFANQ
- revoked Boolean: If the credential is revoked or not. It can be "true" or "false".
- self Boolean: Retrieve credentials of the provided Identifier. It can be "true" or "false".
- query-field String: Retrieve credentials based on the filters applied to the data of the credential.

Note: The "subject" and "self" filters cannot be applied together.

The Issuer Node responds by sending a response message that contains the Verifiable Credential and all the information related to it. The response consists of information related to authclaim (which authorizes the user that requests for credential) and coreclaim (the actual credential issued by an Issuer to the user. Depending on these two claims, the information related to these two may differ in the response body. Here, we are going to provide an overview of some of these fields:

- Context: URL pointing to the JSON-LD documents that define how credential schema (here we are using BJJAuthCredential) and claim-schema-vocab (here we are using SparseMerkleTreeProof)are defined.

- credentialSchema: URL pointing to the credential schema of type JSON. It could be a schema for authclaim or coreclaim.

- credentialStatus: Shows credentialStatus id (which is the Revocation status of the credential (presence or absence of the revocation nonce value), revocationNonce (zero or any value), type(type of Proof, for example, SparseMerkleTreeProof).

- credentialSubject: contains details of the subject (to whom the credential is issued) and includes the subject's date of birth, claim id, documentType, and other details.

- - type: Type of credential for credentialSubject (AuthBJJCredential or KYCAgeCredential)
- id: It is the ID of the Verifiable Credential.
- expiration: The date on which the credential shall expire.
- issuer: DID of the Issuer.
- issuanceDate: The date on which the credential was issued by the Issuer.
- proof: The proof that the user creates to prove that s/he is the real owner of the Verifiable Credential issued from the Issuer and that the Verifiable Credential that it holds is valid. It includes:
  
- - type of proof (for example, BJJSignature2021 or SparseMerkleTreeProof)
- - issuerData: It includes the Issuer's id (DID of the Issuer) and its state (value of its claimstreeroot, i.e. root of the claims (credential) tree)
- - authclaim: Value of authclaim along with its mtp existence (proof of its existence/non-existence in the Merkle tree)
- - coreclaim: Value of coreclaim along with signature (Issuer's signature which verifies that the credential is issued by a valid Issuer).

[API Reference](http://54.179.233.10:3003/#get-/v1/-identifier-/claims)

## Get Claim QR Code
Note: In order to communicate with the Uptick DID Wallet App, the Issuer Node must be hosted on a public URL.

Function: Endpoint to generate a JSON which is then used to generate a QR code on a third-party app. The user can then scan this QR code and accept credentials to his/her wallet.

How it Works: The Issuer DID (identifier string retrieved from calling the Create Identity endpoint) and credential Identifier (or cid retrieved from the Create Claim endpoint) are passed as path variables in the request URL.

The Issuer Node responds by sending a response message that contains a JSON which carries the following fields:

- credentials contains the credential ID (cid) and a link to the schema associated with the credential.

- url is the address at which the user's wallet makes a call to the endpoint.

- from is the did of the Issuer.

- to is the did of the user's wallet.

typ and type indicate the way user's wallet interacts with the Node.

This JSON can then be pasted on a third-party app's interface that supports generating QR codes. Once a QR code is generated, the user can scan it via Uptick DID app on mobile and accept a credential to his/her wallet.

[API Reference](http://54.179.233.10:3003/#get-/v1/-identifier-/claims/-id-/qrcode)

## Revoke Claim
Function: Endpoint to revoke a Verifiable Credential

How it Works: The Issuer DID (The identifier string retrieved from calling the Create Identity endpoint) and nonce of the VC to be revoked (Revocation Nonce) are passed as a path variable in the request URL.

The server responds by showing the Revocation Status of the credential.

[API Reference](http://54.179.233.10:3003/#post-/v1/-identifier-/claims/revoke/-nonce-)

## Get Revocation Status
Function: Endpoint to retrieve the Revocation Status of the Verifiable Credential.

How it Works: The DID (The identifier string retrieved from calling the Create Identity endpoint) and nonce (Revocation Nonce) are passed as a path variable in the request URL. For the credential to be marked "revoked", we need to publish the state first on-chain, and then wait for 5 confirmation blocks.

The server responds by sending the following details:

- issuer
- - claimstreeRoot: Root of the Claims Merkle Tree of the Issuer
- - state: The Issuer's Identity State
- mtp
- - existence: Existence or Non-existence of the Revocation Nonce on the Revocation Merkle Tree. For retrieving the revocation status from this endpoint, we need to first send a transaction and after that, the state is published on-chain. Once that is done, the existence of the revocation nonce on Merkle Tree changes to "true".