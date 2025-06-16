<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Specification of Wallet to Wallet interactions

## Abstract

The present document specifies the common protocols and interfaces according to Article 5a (4) (c) and Article 5a (5) (a) (vi) of
[(EU) No 910/2014](http://data.europa.eu/eli/reg/2014/910/2024-10-18) used by a Wallet Unit to interact with another
Wallet Unit.

### [GitHub Discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/categories/technical-specification-discussion)

## Versioning

| Version | Date       | Description                            |
|---------|------------|----------------------------------------|
| `0.1`   | 13.06.2025 | Initial version for first discussions. |

## 1. Introduction

The present document specifies how two Wallet Units shall interact to exchange PID or other Attestations.

This specification is designed to enable the high level requirements defined in the ARF. Additionally the specification strives to ensure that the Wallet-to-Wallet (W2W) interaction similar to comparable functionality elsewhere in the EUDI Wallet ecosystem. I.e., the mechanism must be compatible with ISO 18013-5. The goal of the specification, is that the solution is technically simple and does not introduce unnecessary complexity.

### 1.1 W2W Use Cases (from ARF HLRs) 
The [Topic J Discussion Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/j-wallet-to-wallet-interactions.md) outlines a single use-case: 
1. Two EUDI Wallet Users meet in physical proximity and agree (out of band of the EUDI Wallet system), that one (the Holder) should present a specific PID (or other attestation), including what data fields, towards the other (the Verifier).
2. Both users select Wallet-to-Wallet mode in their respective EUDI Wallet Unit UI and are asked to specify their role (Holder or Verifier).
3. The Holder is given an option to suggest which PID or Attestations (and which data fields) to share with the Verifier (this information is dubbed a "presentation offer").
4. A handshake protocol is performed and a data connection is established between the two devices. This protocol also sends the presentation offer to the Verifier, if it was specified.
5. If the Holder specified a presentation offer in Step 3, this is now displayed to the Verifier in the EUDI Wallet Unit UI. Based on this, the Verifier specifies, what PID or attestation (and which data fields) should be presented, in the Wallet-to-Wallet presentation, i.e. a presentation request. Note that the presentation request can be created from scratch if there was no presentation offer or it can contain all, or a subset, of the elements specified in the presentation offer if the presentation offer is present.
6. The Verifier's EUDI Wallet sends the presentation request to the Holder
7. The Holder is prompted to share their presentation with the Verifier (i.e. accept the presentation request). Note that the EUDI Wallet will check if the presentation request matches the presentation offer created in step 3.
8. If the Holder approves the presentation on their EUDI Wallet, then a presentation is sent to the Verifier's EUDI Wallet Unit.
9. In the Verifier's EUDI Wallet Unit, the received presentation is verified and presented in the UI.

We will refer to the variation of the Wallet-to-Wallet Interaction where a holder specifies a presentation offer in Step 3 as _holder-driven_ and the variation where no presentation offer is requested as _verifier-driven_. 

### 1.2 Scope
The STS will define how the above standards must be supported by each EUDI Wallet Unit to enable the interactions described above. This will largely follow the ISO 18013-5 standard for mobile driving licenses. 
However, this document will also specify the additional elements and deviations from ISO 18013-5 that are necessary to support W2W interactions for EUDI Wallets: 
1. The _technical format_ for a presentation offer and how it is _transferred_ 
2. The _restrictions_ on creation of presentation requests.
3. How the _integrity_ of the interaction is ensured.

In this initial draft there will be a discussion of the extent to which it is necessary to ensure the integrity of the interaction rather than a specification of this. 

### 1.3 Requirements Notation
The key words "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) [RFC8174](https://datatracker.ietf.org/doc/html/rfc8174) when, and only when, they are written in all capital letters. 

## 2. Specification for W2W Interactions

### 2.1 Terminology 
The following terminology will be used in the specification. 

| Term                 | Meaning                                                                                          |
|----------------------|--------------------------------------------------------------------------------------------------|
| Verifier             | An EUDI Wallet User that wishes to find out attested information about another EUDI Wallet user. |
| Holder               | An EUDI Wallet User that wishes to share attested information with another EUDI Wallet user.     |
| Presentation offer   | A description of the attested information, that a Holder wishes to share with the Verifier.      |
| Presentation request | A description of the attested information, that the Verifier wishes the Holder to present.       |
| Presentation         | The attested information being sent from the Holder to the Verifier.                             |

### 2.2 Overview 
At a high-level W2W interactions shall be performed following ISO 18013-5 device retrieval method letting the Holder's Wallet Unit act as a mDL (cf. ISO 18013-5) and the Verifier's Wallet Unit act as an mDL Reader (cf. ISO 18013-5). 
The `Device Engagement` structure of ISO 18013-5 will be extended to also optionally include the Holder's presentation offer. 
The presentation offer will itself be a `NameSpaces` structure cf. ISO 18013-5:2021 p. 30. 
If present, a presentation offer will impose restrictions on the Verifier's `mdoc request` such that the `DataElements` in such subsequent mdoc request, must be a subset of the `DataElements` in the received presentation offer. 
As a default there will be no authentication of the EUDI Wallets used in the interaction.
If a Verifier wishes to verify the validity of the holder's wallet they can request a PID which acts also a proof of a valid wallet given the revocation chaining requirement related to PID. 

### 2.3 Initialization
Before any information is transferred between the Holder and Verifier, it is important that both user actively accept that data is about to be exchanged in a W2W setting. Furthermore, the users shall be notified about the limitations of the W2W settings compared to the regular Relying Party use case, i.e., it is only intended for natural persons and that the users should consider if the other party is trustworthy (as discussed in Section 2.6).

EUDI Wallet Units SHALL offer the user an option to activate W2W and proceed to Device Engagement.

EUDI Wallet Units SHALL notify the User that W2W functionality should only be used with natural person they trust, before W2W is activated.

EUDI Wallet Units SHALL NOT proceed to Device Engagement before the user has activated W2W.

EUDI Wallet Units SHALL allow the user to choose a role of either Holder or Verifier.


### 2.4 Device Engagement
Device engagement for EUDI Wallets to do W2W interactions SHALL follow ISO 18013-5 with the extensions and restrictions described in this section. 
The Holder's EUDI Wallet SHALL take the role of a mDL and the Verifier's EUDI Wallet SHALL take the role of a mDL reader. 

#### 2.4.1 Device Engagement Technologies
EUDI Wallet Units SHALL support QR codes for engaging as a holder in a W2W interaction. 
EUDI Wallet Units SHALL support QR codes for engaging as a verifier in a W2W interaction. 

> Rationale: As NFC is not supported by all smartphones it is necessary to mandate a engagement technology that works across all smartphones. 

EUDI Wallet Units MAY support NFC for engaging as a holder in a W2W interaction. 
EUDI Wallet Units MAY support NFC for engaging as a verifier in a W2W interaction. 

#### 2.4.2 Presentation Offer
The Holder's EUDI Wallet SHALL allow the Holder to select a set attributes available in their already issued credentials. 
If such selection is made, the Holder's EUDI Wallet SHALL include them in a `PresentationOffer` under the key `-1` in the `DeviceEngagement` structure. 

The `DeviceEngagement` structure used for W2W interactions shall have the following form: 
```
DeviceEngagement = {
	0: tstr, ; Version
	1: Security,
	? 2: DeviceRetrievalMethods, ; Is absent if NFC is used for device engagement
	? 3: ServerRetrievalMethods,
	? 4: ProtocolInfo,
	? -1: PresentationOffer
	* int => any
}
```
All fields follow ISO 18013-5 except `PresentationOffer` offer which shall be defined as: 

```
PresentationOffer = {
	"docsOffer" : [+ DocsOffer]
}

DocsOffer = {
	"itemsOffer" : ItemsOfferBytes
}

ItemsOfferBytes = #6.24 (bstr .cbor ItemsOffer)

ItemsOffer = {
	"docType" : DocType ; Document Type offered. 
	"nameSpaces : NameSpaces
}
```

`docsOffer` contains an array of all offered documents. 

`ItemsOfferBytes` contains the `ItemsOffer` structure as tagged CBOR bytestring. 

`DocType` and `NameSpaces` follows the ISO 18013-5:2021 definition for mdoc requests (p. 29-30). 

The `IntentToRetain`  field within the `DataElements` of the `NameSpace` structure SHALL be set to `false`.

> Note that the `PresentationOffer` is similar to the `DeviceRequest` structure of ISO 18013-5:2021 Section 8.3.2.1.2.1.

Below is an example of a presentation offer using CBOR Diagnostic Notation (CDN)
```
{
	"docOffer": [{
	"itemsOffer":
		24(<< {
			"docType": "org.iso.18013.5.1.mDL",
			"nameSpaces": {
				"org.iso.18013.5.1": {
					"family_name": false,
					"document_number": false,
					"driving_privileges": false,
					"issue_date": false,
					"expiry_date": false,
					"portrait": false,
					}
				}
			}
		>>)
	}]
}
```

### 2.5 Data Retrieval 
Data retrieval for EUDI Wallets to do W2W interactions SHALL follow ISO 18013-5 with the extensions and restrictions described in this section. 

#### 2.5.1 Data Retrieval Transmission Technologies 
EUDI Wallets SHALL support device retrieval.
EUDI Wallets SHALL NOT support server retrieval. 

EUDI Wallet Units SHALL support BLE for data retrieval as a holder in a W2W interaction. 
EUDI Wallet Units SHALL support BLE for data retrieval as a verifier in a W2W interaction. 

> Rationale: As NFC is not supported by all smartphones it is necessary to mandate a data retrieval technology that works across all smartphones. 

EUDI Wallet Units MAY support NFC for data retrieval as a holder in a W2W interaction. 
EUDI Wallet Units MAY support NFC for data retrieval as a verifier in a W2W interaction. 

EUDI Wallet Units MAY support WiFi Aware for data retrieval as a holder in a W2W interaction. 
EUDI Wallet Units MAY support WiFi Aware for data retrieval as a verifier in a W2W interaction. 

#### 2.5.2 Requesting Data 
The EUDI Wallet SHALL enable the Verifier to construct an mdoc request as follows: 
- If a presentation offer is present in the Device Engagement information received, then the Verifier SHALL be allowed to select a subset of the DataElements to include in their mdoc request. 
- If a presentation offer is not present in the Device Engagement information received, then the Verifier SHALL be allowed to select a set of the `DataElements` to include in their mdoc request without restrictions.

The `IntentToRetain` flag SHALL be set to `false` in a mdoc request used in a W2W interaction. 

The optional `ReaderAuth` field SHALL NOT be included in an mdoc request part of a W2W interaction. 
> For a discussion of this see (#26-integrity-of-the-interaction). 

#### 2.5.3 Presenting Data
If a presentation offer was sent as part of the W2W interaction, then the Holder's EUDI Wallet receiving an mdoc request SHALL verify that the `DataElements` in the mdoc request is a subset of the `DataElements` sent in the presentation offer. If not, then the holder's EUDI Wallet SHALL abort the interaction. 

> Note: The details of how to abort is to be described in later iterations of this document. 


### 2.6 Integrity of the Interaction
The ARF specifies (in W2W\_09 and W2W\_10), that each Wallet Unit must verify the validity of the other Wallet Unit when they receive a presentation or presentation request. 

These requirements correspond to similar requirements elsewhere in the ARF, where parties interact with each other, e.g. in the regular Relying Party use case, the Wallet Unit must verify that the Relying Party is on the Trusted List and the Relying Party must authenticate the Wallet Unit.

Wallet Unit Attestations (WUA) and Wallet App Attestations (WAA) solve this challenge in relation to issuance of new Attestations or PID. Furthermore, as revocation chaining is mandatory in relation to PID (the PID provider must revoke any issued PID, if the Wallet Unit to which the PID was issued, is revoked), simply presenting a valid PID is sufficient for the verifier to authenticate the Wallet Unit. Hence, if a Wallet Unit presents a PID W2W\_10 is achieved. Note that the PID presentation doesn't need to disclose any attributes of the PID and merely possession of any PID is sufficient. If the presentation request is for a non-PID attestation, the Verifier may choose to include a PID presentation in the Presentation request to achieve the authentication of the Holder Wallet Unit.

With regard to the Holder authenticating the Verifier Wallet Unit, a similar approach can be taken, i.e. the Verifier sends a PID presentation to the Holder Waller Unit. However, we would argue that this level of authentication is not needed, as it would provide a highe<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Specification of Wallet to Wallet interactions

## Abstract

The present document specifies the common protocols and interfaces according to Article 5a (4) (c) and Article 5a (5) (a) (vi) of
[(EU) No 910/2014](http://data.europa.eu/eli/reg/2014/910/2024-10-18) used by a Wallet Unit to interact with another
Wallet Unit.

### [GitHub Discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/categories/technical-specification-discussion)

## Versioning

| Version | Date       | Description                            |
|---------|------------|----------------------------------------|
| `0.1`   | 13.06.2025 | Initial version for first discussions. |

## 1. Introduction

The present document specifies how two Wallet Units shall interact to exchange PID or other Attestations.

This specification is designed to enable the high level requirements defined in the ARF. Additionally the specification strives to ensure that the Wallet-to-Wallet (W2W) interaction similar to comparable functionality elsewhere in the EUDI Wallet ecosystem. I.e., the mechanism must be compatible with ISO


## Appendix A. Requirements

### A.1 Legal Requirements about Wallet to Wallet Interactions

**[European Digital Identity Regulation] Article 5a European Digital Identity Wallets**

 ...4. European Digital Identity Wallets shall enable the user, in a manner that is user-friendly, transparent, and traceable by the user, to: 

... (c) securely authenticate another person’s European Digital Identity Wallet, and receive and share person identification data and electronic attestations of attributes in a secured way between the two European Digital Identity Wallets;... 

...5.   European Digital Identity Wallets shall, in particular:

(a) support common protocols and interfaces:...

... (vi) for interaction between two persons’ European Digital Identity Wallets for the purpose of receiving, validating and sharing person identification data and electronic attestations of attributes in a secure manner;...


**[CIR 2024/2982] Article 3 General Provisions** 

Regarding the protocols and interfaces referred to in Articles 4 and 5, wallet providers shall ensure that wallet units:...

(2) authenticate and validate the wallet unit attestations of other wallet units where interacting with other wallet units;...
(8) present wallet unit attestations of the wallet unit to wallet-relying parties or wallet units that request it;...

**[CIR 2024/2982] Article 5 Presentation of attributes to wallet-relying parties**

1.   Wallet providers shall ensure that wallet solutions support protocols and interfaces for the presentation of attributes to wallet-relying parties, remotely, and where appropriate in proximity, in accordance with the standards set out in the Annex.

2.   Wallet providers shall ensure that, at the request of users, wallet units respond to successfully authenticated and validated requests from wallet-relying parties referred to in Article 3, in accordance with the standards set out in the Annex.

3.   Wallet providers shall ensure that wallet units support proving the possession of private keys corresponding to public keys used in cryptographic bindings.

4.   Wallet providers shall ensure that wallet solutions support the selective disclosure of attributes of personal identification data and of electronic attestations of attributes.

5.   Paragraphs 1 to 4 shall apply mutatis mutandis to interactions between two wallet units in proximity.


### A.2 High-Level Requirements

The set of high-level requirements after the discussions documented in
the [Topic J Discussionq Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/j-wallet-to-wallet-interactions.md) 
are as follows:

| **Index** | **Requirement specification**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| W2W\_01   | A Wallet Unit SHALL support an interface and protocol for, in close proximity to another Wallet Unit to: - Establish a connection with the other Wallet Unit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| W2W\_02   | A Wallet Unit SHALL support functionality for, using the connection established in Requirement 1 to: - Receive PID and (Q)EAA presentation requests from the other Wallet Unit, - Respond to such requests.                                                                                                                                                                                                                                                                                                                                                                                                                           |
| W2W\_03   | A Wallet Unit SHOULD provide the User with the option, using a user-friendly UI, to suggest to the other Wallet Unit, which attestations and attributes it can include in a presentation request, i.e., a presentation offer. If the User uses the functionality, the suggestion SHALL be transferred as part of the protocol mentioned in W2W_01.                                                                                                                                                                                                                                                                                    |
| W2W\_04   | A Wallet Unit SHALL support functionality for, using the connection established in Requirement 1: - Create and send PID and (Q)EAA presentation requests to the other Wallet Unit. - If a presentation offer is present, it SHALL be used to create the presentation request. The Verifier MAY choose to only request a subset of the suggested attributes. - If a presentation offer is not present, the Wallet Unit SHALL allow the Verifier to, using a user-friendly UI, specify which attributes to request in a presentation request. - Receive, validate, and present the corresponding presentation response to the Verifier. |
| W2W\_05   | If the Holder makes a suggestion for a presentation request, i.e., a presentation offer, then the Wallet Unit of the Verifier SHALL NOT allow the Verifier to include different attestations and/or more attributes in their presentation request sent to the Holder.                                                                                                                                                                                                                                                                                                                                                                 |
| W2W\_06   | A Wallet Unit SHALL only establish a connection with another Wallet Units if both Users of the Wallet Units take explicit actions to make this happen. In doing so, it SHALL be possible for the User to indicate which other device a connection is to be established with.                                                                                                                                                                                                                                                                                                                                                          |
| W2W\_07   | The Holder Wallet Unit SHALL authenticate the User and obtain the User's approval, as specified in, before presenting requested attestations or attributes to another Wallet Unit. The Wallet Unit SHALL inform the User about the attributes that are being requested.                                                                                                                                                                                                                                                                                                                                                               |
| W2W\_08   | The Verifier Wallet Unit SHALL authenticate its User and obtain the User's approval prior to requesting attestations presentation from another Wallet Unit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| W2W\_09   | The Holder Wallet Unit SHALL verify the validity of the Verifier Wallet Unit before presenting a received request to the User.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| W2W\_10   | The Verifier Wallet Unit SHALL verify the validity of the Holder Wallet Unit before presenting the received presentation to the user.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| W2W\_11   | The Holder Wallet Unit SHALL make it clear to the user whether they are presenting to a Relying Party or another natural person's EUDI Wallet before the Holder is to approve their presentation. The Holder SHALL be advised to stop the presentation in case they were expecting to interact with a legal entity different from a natural person but are informed that the presentation will be towards a natural person.                                                                                                                                                                                                           |
| W2W\_12   | Wallet Units SHALL take measures to limit the number of Wallet-to-Wallet presentations per time unit to prevent large-scale abuse of the wallet-to-wallet functionality. The limitation strategy, e.g. exponential backoff time between subsequent presentations or hard limits, is decided by the Wallet Provider.                                                                                                                                                                                                                                                                                                                   |
| W2W\_13   | A Holder Wallet Unit SHALL not persistently store the content of a received presentation. The Holder Wallet Unit SHOULD minimize the time the received presentation is stored in memory.                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| W2W\_14   | Wallet Providers SHOULD take measures to prevent a Verifier from taking screenshots of a received presenation in their Wallet Units.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |





