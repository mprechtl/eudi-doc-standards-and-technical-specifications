<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Common Set of Relying Party Information to be Registered

## Abstract

The present document specifies the common data set required for Relying Party Registration as required by the [European Digital Identity Regulation (EU 2024/1183)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183).

### [GitHub discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/358)

## Versioning

| Version | Date        | Description               |
|---------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `0.1`   | 25.04.2025  | Initial version for first discussion                      |


## 1. Introduction and Overview

The present document specifies the minimum common data set required from the Wallet-Relying Parties in **Relying Party Registration** according to [**European Digital Identity Regulation (EU 2024/1183)**](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183) ("[Regulation]") and the Commission Implementing Regulation
(CIR) [**EU 2025/TBA**](https://eur-lex.europa.eu/eli/reg_impl/2025/TBA/oj) of 21 May 2025 on laying down rules for the application of Regulation (EU) No 910/2014 of the European Parliament and of the Council as regards the registration of wallet-relying parties ("[CIR for Relying Party Registration]"). 

Relying Party Registration is addressed in Article 5b (11) of the Regulation and the [CIR for Relying Party Registration] lays down further details, which are in scope of present technical document: The registration of a Relying Party to a registry shall include at least the information set out in Annex I of the [CIR for Relying Party Registration] (see Art. 3(2)). 

## 2 Common Data Set

### 2.1 Mandatory Data Set in Registration

This section provides a table of minimum set of attributes, that SHALL be provided in Wallet-Relying Party registration based on ruling of [CIR for Relying Party Registration] Annex I. 

For guidance on the data format of each information attribute, the table rows contain a reference to the corresponding class and attribute [class.element] definition specified in [Common formats and API specification] and [Provider information specification], whichever applies.

The Description row in the table contains clarifications regarding use of mandatory attributes with various Wallet-Relying Party types (entitlements and provided attestations ('sub-entitlements' in ).

| Data Identifier             |  Description            | Class reference     |
|-----------------------------|-------------------------|---------------------|
| Legal Name        | Name of the Wallet-Relying Party in an official record.   | WalletRelyingParty.legalName |
| Trade Name | A user-friendly name (trade name or service name) of the Wallet-Relying Party. SHALL be used as name of the entity if Legal Name is not applicable.   |   WalletRelyingParty.tradeName |
| Identifier  | Official identifier of the entity. **If several are registered to the Registry, at least one Identifier SHALL be of type 'EUID'** (see \[[CIR for BRIS\]](https://eur-lex.europa.eu/eli/reg_impl/2021/1042/oj)) for availability of an unique identifier of the Wallet-Relying Party for technical purposes.   |    WalletRelyingParty.Identifier    |
| Physical Address  | The postal address where the Wallet-Relying Party is established SHALL be provided for the Registrar.  | WalletRelyingParty.postalAddress | WalletRelyingParty.postalAddress     |
|  Contact Information    | a Wallet-relying Party SHALL provide detailed contact information pertaining to its registration and intended use of the wallet units in form of **at least one of** (a) a website for helpdesk and support, (b) a phone number or (c) an e-mail address.    |   WalletRelyingParty.supportURI  WalletRelyingParty.phone  WalletRelyingParty.email   |
| Service Description   |    A description of the service the wallet-relying party provides.   |  WalletRelyingParty.srvDescription      |
| Public Sector Body    | An indication whether the Wallet-Relying Party is a public sector body.  | WalletRelyingParty.isPSB      |
| Entitlements  |  The entitlement(s) of the Wallet-Relying Party. Possible entitlement types are ‘Service_Provider’, ‘QEAA_Provider’, ‘Non_Q_EAA_Provider’, ‘PUB_EAA_Provider’, ‘PID_Provider’, ‘QCert_for_ESeal_Provider’, ‘QCert_for_ESig_Provider’, ‘rQSigCDs_Provider’, ‘rQSealCDs_Provider’ or ‘ESig_ESeal_Creation_Provider’.  | WalletRelyingParty.entitlement |
| Purpose | A description of intended use of the data that the wallet-relying party intends to request from wallet units. **Provided for each intended use.**    |    WalletRelyingParty.IntendedUse.purpose       |
| Data Requested    | Provided the Wallet-Relying Party intends to request data from the Wallet Unit for its intended use, the attestations and attributes intends to request. Provided from the registry in a machine readable format that provides the attestation and attribute names & types. **Provided for each intended use.**   |  WalletRelyingParty.IntendedUse.Credential    |
| Data Protection Authority | The competent data protection supervisory authority supervising the Wallet-Relying Party and its intended uses. The national Registry SHALL arrange access to the national DPA(s)s with their Contact Information in the mechanism offered for registering Wallet-Relying Parties. **Provided for each intended use of the Wallet-Relying Party.** | WalletRelyingParty.supervisoryAuthority |

## 2.2 Optional Data to be Provided in Registration

This section provides a table of attributes, that MAY be provided in wallet-relying party registration based on ruling of [CIR for Relying Party Registration] Annex I. For guidance on the data format of each information attribute, the table rows contain a pointer to the corresponding class and attribute [class.element] definition specified in [Common formats and API specification] and [Provider information specification].

The table contains clarifications regarding use of the data attributes when the End-Relying Parties have attestation provider entitlement(s) or are as service providers relying on the option of using an Intermediary (a Wallet-Relying Party registered to operate as an Intermediary) when transacting with the EUDI wallets.

| Data Identifier             |  Description            | Class reference     |
|-----------------------------|-------------------------|---------------------|
| infoURI  | At least one uniform resource identifier (‘URI’) belonging to the wallet-relying party with web page(s) for information about the entity, if applicable. | WalletRelyingParty.infoURI   |
| Identifier  | Alternate identifiers of the entity than the type 'EUID' Identifier (See [Mandatory Data Set in Registration](#21)).  |    WalletRelyingParty.Identifier    |
| Use of Intermediary   | List of Intermediaries the Wallet-Relying Party with entitlement type 'Service_provider' relies upon, if it is not transacting directly with the Wallet Units. When present,Intermediary-specific data object SHALL contain its unique identifier of type 'EUID' and its Trade name.   | WalletRelyingParty.usesIntermediary  |
| isIntermediary | An indicator flag that SHALL be present if the registering Wallet-Relying Party intends to act as an Intermediary for other (End-) Wallet-Relying Parties. *Note: If the same Wallet-Relying Party provides a service which requires presentation of attestations, it SHALL register this service as a separate registration instance, with appropriate data on its intended use(s).*    | WalletRelyingParty.isIntermediary
| providesAttestations | A list of attestations a Wallet-Relying Party intends to issue for Wallet Units. SHALL only be present if the entitlements of the Wallet-Relying Party are of type ‘QEAA_Provider’, ‘Non_Q_EAA_Provider’, ‘PUB_EAA_Provider’ or ‘PID_Provider’. | WalletRelyingParty.providesAttestations


## 4. References

| Reference      | Description |
|----------------------------------------|----------------------------------------------|
| [Regulation] | [European Digital Identity Regulation (EU 2024/1183)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183)  |
| [CIR for Relying Party Registration]      | **Link TBA**[]()  |
| [CIR for BRIS]  | [CIR (EU) 2021/1042 on technical specifications and procedures for the system of interconnection of registers](https://eur-lex.europa.eu/eli/reg_impl/2021/1042/oj)      |
| [Provider information specification]      | [Specification of systems enabling the notification and subsequent publication of Provider information](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts2-notification-publication-provider-information.md) |
| [Common formats and API specification]           | [Specification of common formats and API for Relying Party Registration information](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration.md)                                     |
