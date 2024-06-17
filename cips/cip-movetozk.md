---
title: 
description: Decision to move from previously planned fraud proofs to zk proofs
author: Nashqueue (@Nashqueue)
discussions-to: URL
status: Draft
type: Standards Track
category: Core
created: 2024-02-28
requires: CIP number(s). Only required when you reference an CIP in the `Specification` section. Otherwise, remove this field.
---


## Abstract

Celestia currently has an implementation of bad encoding fraud proofs. To have fully trust minimized light clients we need to have a fraud proof for every protocol rule that can be broken. Many of these rules can be fraud proven by the same fraud proof. Instead of having to implement a new fraud proof for every rule we can move to zk proofs as zk-proofs have matured enough since the inception of the initial specification of the Celestia protocol. This will allow us to have a fully trust minimized light client but also make the protocol design more simple. This CIP should not decide which protocol rule will be zk-proven first just the fact that it will be zk-proven in the future. From that decision alone we can follow through with multiple CIPs simplifying the protocol in anticipation of a zk-proof. 

## Motivation

The Celestia protocol which is based on the "Lazy Ledger" whitepaper and "Fraud Proofs: Maximising Light Client Security and Scaling Blockchains with Dishonest Majorities" always had in mind to create trust minimized light clients. The way the protocol aims to achieve this is by using fraud proofs and having a one honest full node assumption. At the time of developing the protocol this approach seemed reasonable and the implementation of [bad encoding fraud proofs (BEFPs)](https://github.com/celestiaorg/celestia-node/blob/v0.11.0-rc3/docs/adr/adr-006-fraud-service.md#detailed-design) proved the concept to be sound and scalable.

5 years have passed since the Lazy Ledger whitepaper and in the meantime zk technology has advanced so much that we should revisit the original decision and move from fraud proofs to zk proofs. 

## Specification

The current [specification](https://celestiaorg.github.io/celestia-app/specs/fraud_proofs.html) on fraud proofs oulines 3 possible fraud proofs.
- Bad encoding fraud proofs
- Blob inclusion fraud proofs
- State fraud proofs
The next subsections will outline the fraud that is captured by each fraud proof.

### Bad encoding fraud proofs
- Bad erasure encoding of a row or column
- Mismatch of a row root of shares proven against the row root and the same shares proven against a column root.
- Wrong share encoding format
- Bad namespace merkle tree generation
- Wrong namespaces in the shares

### Blob Inclusion fraud proofs
- Length of the blob specified in the PFB does not match the length specified in the first share of the blob
- Actual length of the blob does not match the length specified
- Index of the blob does not match the index specified
- Blob length would go outside of the max square size
- Blob is not included even though the PayForBlob transaction exists
- 2 PayForBlob transactions point to the same blob

### State fraud proofs
- Wrong intermediate state root after a certain amount of transactions

### Possible fraud not captured by those types of fraud proofs 
- Wrong amount of padding shares using the Non-Interactive rules
- Bad encoding of PFB-metadata in PFB-transactions 
- Bad encoding of compact shares in the reserved namespace
- Wrong transaction index incrementation
- Too many or too little intermediate stateroots
- Wrong use of a reserved namespace, that might be reserved but not should not be occupied
- Wrong amount of fees paid by the user to the protocol

Moving from fraud proofs to zk proofs, every fraud must be either eliminated by removing that feature or have its correctness proven with a zk proof. The scope of a fraud proof can be reduced by proving the correctness of a subset that a fraud proof is supposed to capture using a zk-proof. An intermediate step is to have most of th fraud captured by a zk-proof, with only a subset not captured, thus limiting the scope of the fraud proof.

Compact fraud proofs exist so light clients don`t have to download the whole block to verify that fraud has happened. This requires Celestia to generate metadata to be able to convey enough information to generate a compact fraud proof. If the intention of moving to zk-proofs is accepted,then it will allow us to remove the metadata and make Celestia more simple.

## Rationale

The rationale 

The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages.

The current placeholder is acceptable for a draft.

TODO: Remove the previous comments before submitting

## Backwards Compatibility

Moving from non-existent fraud proofs for specific validity rules to be validated by zk-proofs does not break the client. Moving erasure encoding validity from fraud proofs to zk-proofs will break client and light node operators that rely on the fraud proofs. This particular migration should be done with caution.

## Test Cases

Normally a test should include this scenario: If there is fraud proof for rule X then there should be a zk proof for rule X. This is not included in this section because it is not possible to test that with the current fraud proofs implementation except for bad encoding fraud proofs. Other fraud proofs would have to be implemented first, which is what the CIP is trying to avoid. Therefore cases would follow depending on the particular protocol changes in the future.

## Security Considerations

The security guarantees for light node operators does not decrease if we move from non-existent fraud proofs to zk-proofs. Depending on what system is used to generate the zk-proofs it might add additional trust assumptions. How a zk-proof is generated should be discussed separately for each validity rule that we are tackling.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE).
