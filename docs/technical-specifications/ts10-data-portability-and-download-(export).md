
<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Data Portability and Download (Export)

## Abstract

The present document specifies the common format and data set for the transaction log and the Migration Object, as well as protocols related to exporting those objects from a Wallet Unit and importing to another one in a backup, recovery and migration scenarios, as required by the [European Digital Identity Regulation (EU 2024/1183)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183) and [CIR 2024/2979](https://eur-lex.europa.eu/eli/reg_impl/2024/2979/oj/eng).

### [GitHub discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/407)

## Versioning

| Version | Date        | Description                              |
|---------|-------------|------------------------------------------|
| `0.1`   | 27.06.2025  | Initial version for the first discussion |
| `0.2`   | 14.07.2025  | Update after the first focus meeting     |
| `0.3`   | 24.07.2025  | Update after the second focus meeting    |

## 1 Introduction

### 1.1 Overview

According to [Regulation (EU) No 910/2014] with the amending [Regulation (EU) 2024/1183] (hereinafter together referred as the [European Digital Identity Regulation]) and [CIR on Integrity and Core Functionalities], the Wallet Units shall keep a transaction log, as well as a "migration object" that can be used to export User's data from a Wallet Unit and to import it to another Wallet Unit, in backup, recovery and migration processes.
The relevant requirements are set out in Article 5a(4)(f) and (g) of the [European Digital Identity Regulation] and Article 9 and 13 of the [CIR on Integrity and Core Functionalities].

### 1.2 Scope

The present document specifies:

- the common format and content for the transaction log,
- the common format and content for the migration object,
- security measures to protect confidentiality, integrity and authenticity of the transaction log and the migration object, in storage, backup, recovery and migration processes.

The migration object contains:

- a file containing the list of credentials (PIDs and atestations) present in a Wallet Unit,
- a transaction log file.

### 1.3 Requirements Notation

The key words "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC2119 RFC8174 when, and only when, they are written in all capital letters.

## 2 Data Model

The present data model has been created based on the legal requirements of [European Digital Identity Regulation] (including the related implementing acts),  and [ARF], while keeping the [SEMIC Style Guide](https://semiceu.github.io/style-guide/1.0.0/index.html) in mind and aligning it with the [Core Business Vocabulary](https://semiceu.github.io/Core-Business-Vocabulary/releases/2.2.0/) as much as reasonable.

![MigrationObjectAndTransactionLogDataModel](img/ts10-eudiw-mig-obj-trans-log-datamodel.svg)

As outlined in the above figure, it contains the following **main classes**:

- **Transaction**
- **Presentation**
- **PseudonymPresentation**
- **W2WPresentationRequest**
- **W2WPresentation**
- **CredentialIssuance**
- **CredentialDeletion**
- **PseudonymGeneration**
- **PseudonymDeletion**
- **CertificateIssuance**
- **CertificateDeletion**
- **SigningSealing**
- **DataDeletionRequest**
- **DPAClaim**
- **OtherTransaction**
- **TransactionLog**
- **TransactionLogObject**
- **ListOfCredentials**
- **MigrationObject**

These classes are defined using the following **auxiliary classes**:

- **TransactionIdentifier**
- **CredentialInfo**
- **ClaimInfo**
- **ListOfClaims**
- **Pseudonym**

These classes depend on the following auxiliary classes specified in the [Relying Party registration specification] and the [Provider information specification]:

- **Credential**
- **Claim**
- **LegalEntity**
- **LegalPerson**
- **NaturalPerson**
- **IntendedUse**
- **Identifier**
- **MultiLangString**
- **Policy**

## 3 Specification

### 3.1 Transaction

The `Transaction` class is the central class of the transaction log data model and it assembles the generic attributes related to a transaction performed with or by a Wallet Unit, relevant for the transaction log creation. It contains the attributes specified in the following table:

| Attribute          | Multiplicity | Type  | Description  |
|--------------------|--------------|-------|--------------|
| `presentation`      | [0..1]       | [*Presentation*](#32-presentation)      | specifies the specific attributes of a **presentation** transaction. This attribute is present, if and only if the transaction is a presentation transaction.        |
| `pseudonymPresentation`      | [0..1]       | [*PseudonymPresentation*](#33-pseudonympresentation)      | specifies the specific attributes of a **pseudonym presentation** transaction. This attribute is present, if and only if the transaction is a presentation performed with use of the User's pseudonym.        |
| `w2wPresentationRequest`      | [0..1]       | [*W2WPresentationRequest*](#34-w2wpresentationrequest)      | specifies the specific attributes of a **presentation request** sending (in-bound) transaction.  This attribute is present, if and only if the transaction is sending a presentation request and logged in the transaction log of the requestors Wallet Unit.        |
| `w2wPresentation`      | [0..1]       | [*W2WPresentation*](#35-w2wpresentation)      | specifies the specific attributes of a **presentation** transaction performed by the Wallet Unit upon request received from another Wallet Unit, in the wallet-to-wallet interaction scenario.  This attribute is present, if and only if the transaction is a presentation transaction in wallet-to-wallet mode.        |
| `credentialIssuance`      | [0..1]       | [*CredentialIssuance*](#36-credentialissuance)      | specifies the specific attributes of a **credential (attestation) issuance** transaction. This attribute is present, if and only if the transaction is credential issuance or re-issuance.        |
| `credentialDeletion`      | [0..1]       | [*CredentialDeletion*](#37-credentialdeletion)      | specifies the specific attributes of a **credential deletion** transaction. This attribute is present, if and only if the transaction is deletion of a credential from the Wallet Unit.        |
| `pseudonymGeneration`      | [0..1]       | [*PseudonymGeneration*](#38-pseudonymgeneration)      | specifies the specific attributes of a **pseudonym generation** transaction. This attribute is present, if and only if the User generates a pseudonym in the Wallet Unit.        |
| `pseudonymDeletion`      | [0..1]       | [*PseudonymDeletion*](#39-pseudonymdeletion)      | specifies the specific attributes of a **pseudonym deletion** transaction. This attribute is present, if and only if the User deletes a previously generated pseudonym in the Wallet Unit.        |
| `certificateIssuance`      | [0..1]       | [*CertificateIssuance*](#310-certificateissuance)      | specifies the specific attributes of a **certificate issuance and/or key pair generation** transaction. The certificate and the associated key pair can be used for signing or sealing. This attribute is present, if and only if the transaction is about signing or sealing certificate issuance and/or associated key pair generation. This attribute is mandatory if the certificate and/or the key pair is generated, stored or processed (during the issuance/generation) inside the Wallet Unit.        |
| `certificateDeletion`      | [0..1]       | [*CertificateDeletion*](#311-certificatedeletion)      | specifies the specific attributes of a **certificate and/or key pair deletion** transaction. This attribute is present, if and only if the transaction is about (for signing or sealing) certificate and/or key pair deletion .        |
| `signingSealing`      | [0..1]       | [*SigningSealing*](#312-signingsealing)      | specifies the specific attributes of a **signing or sealing** transaction.  This attribute is present, if and only if the transaction is about signature or seal creation by the user with use of the Wallet Unit.        |
| `dataDeletionRequest`      | [0..1]       | [*DataDeletionRequest*](#313-datadeletionrequest)      | specifies the specific attributes of a **data deletion request** transaction.  This attribute is present, if and only if the transaction is about sending a request to a wallet-Relying Party to delete User's data previously obtained from the User's Wallet Unit with a presentation transaction.        |
| `dpaReport`      | [0..1]       | [*DPAReport*](#314-dpareport)      | specifies the specific attributes of a **data protection authority report** transaction.  This attribute is present, if and only if the transaction is about sending a report to a data protection authority by the User directly from the Wallet Unit.        |
| `otherTransaction`      | [0..1]       | [*OtherTransaction*](#315-othertransaction)      | specifies the specific attributes of **other** types of transaction logged in the transaction log.  This attribute is present, if and only if the transaction is none of the above mentioned transaction types.        |
| `transactionIdentifier`      | [1..1]       | *string*      | contains the **transaction identifier** of the transaction processed by the Wallet Unit.        |
| `time`      | [1..1]       | *string*      |           contains the **date and time** of the transaction in ISO 8601 format (YYYY-MM-DDTHH:mm:ss).              |
| `transactionType`      | [1..1]       | *string*     |           contains the **type** of the transaction, where the following values are defined  in the present document: <ul><li>`Presentation`</li><li>`PseudonymPresentation`</li><li>`W2WPresentation`</li><li>`W2WPresentationRequest`</li><li>`CredentialIssuance`</li><li>`CredentialReissuance`</li><li>`CredentialDeletion`</li><li>`PseudonymGeneration`</li><li>`PseudonymDeletion`</li><li>`CertificateIssuance`</li><li>`CertificateDeletion`</li><li>`SigningSealing`</li><li>`DataDeletionRequest`</li><li>`DPAReport`</li><li>`OtherTransaction`</li></ul>         |
| `transactionResult`     | [1..1]       | *string*      |         specifies the **result** of the transaction, where the following values are defined in the present document: <li>`Completed`</li><li>`NotCompleted`</li>         |

### 3.2 Presentation

The `Presentation` class is used within the definition of [`Transaction`](#31-transaction) and specifies the attributes related to the presentation requests received and processed by the Wallet Unit (including pseudonym transactions). It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [0..1]       | *[Identifier](#32011-identifier-external)*       |   may be present in order to specify one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.  Not present if `interactingPartyType` is natural person in a wallet to wallet interaction scenario.      |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12, plus additional ones): <li>`ServiceProvider`</li><li>`QEAAProvider`</li><li>`NonQEAAProvider`</li><li>`PubEEAProvider`</li><li>`PIDProvider`</li><li>`QCertForESealProvider`</li><li>`QCertForESigProvider`</li><li>`rQSigCDProvider`</li><li>`rQSealCDProvider`</li><li>`ESigESealCreationProvider`</li><li>`NaturalPerson`</li>              |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification].   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following attributes: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification]. If the interacting party is a legal person, then the `LegalEntity.postalAddress` attribute is not present.     |  
| `purpose`       | [1..*]       |   [*MultiLangString*](#32012-multilangstring-external)    |        specifies one or more purposes of the intended data processing according to Article 5 1. (b) of (EU) 2016/679 as `IntendedUse.purpose` attribute specified in [Provider information specification].                   |
| `privacyPolicy`    | [1..*]       | *[Policy](#32013-policy-external)* |     specifies one or more purposes of the intended data processing according to Article 5 1. (b) of (EU) 2016/679, and equals to `IntendedUse.privacyPolicy` attribute specified in [Provider information specification].            |
| `typeOfData`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the data involved in a transaction, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `listOfClaimsRequested`      | [1..*]       | Array of [*ClaimInfo*](#3202-claiminfo) objects      | contains an array with information about all the claims (Wallet Unit User-related attributes) requested from the User's Wallet Unit by an interacting party.     |
| `listOfClaimsPresented`      | [1..*]       | Array of [*ClaimInfo*](#3202-claiminfo) objects      | contains an array with information about all the claims (User-related attributes) presented by the User's Wallet Unit to an interacting party. (_Note: the User may choose to release only some of the requested attributes._)     |
| `reasonOfNoncompletion`    | [0..1]       | *string*      |       specifies the **reason of non completion** of a transaction; included if and only if `TransactionResult` value is `NotCompleted`.                            |
| `registrarURL`    | [1..1]       | *string*      |       specifies a Unique Resource Locator (URL) with a **link to the registrar's on-line service**, where the registration information about the interacting party can be retrieved from.                            |

### 3.3 PseudonymPresentation

The `PseudonymPresentation` class is used within the definition of [`Transaction`](#31-transaction) and specifies the attributes related to the presentation requests received and processed by the Wallet Unit (including pseudonym transactions). It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [0..1]       | *[Identifier](#32011-identifier-external)*       |   may be present in order to specify one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.  Not present if `interactingPartyType` is natural person in a wallet to wallet interaction scenario.      |
| `interactingPartyType`       | [0..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12, plus additional ones): <li>`ServiceProvider`</li><li>`QEAAProvider`</li><li>`NonQEAAProvider`</li><li>`PubEEAProvider`</li><li>`PIDProvider`</li><li>`QCertForESealProvider`</li><li>`QCertForESigProvider`</li><li>`rQSigCDProvider`</li><li>`rQSealCDProvider`</li><li>`ESigESealCreationProvider`</li><li>`NaturalPerson`</li>. This attribute is optional (depends on whether the Wallet Unit User decides to verify the interacting party's identity).               |
| `interactingPartyName`      | [0..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification]. This attribute is optional (depends on whether the Wallet Unit User decides to verify the interacting party's identity).   |
| `interactingPartyContact`      | [0..1]  |  Array of *string* objects      | contains an array with the following attributes: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification]. If the interacting party is a legal person, then the `LegalEntity.postalAddress` attribute is not present. This attribute is optional (depends on whether the Wallet Unit User decides to verify the interacting party's identity).     |  
| `reasonOfNoncompletion`    | [0..1]       | *string*      |       specifies the **reason of non completion** of a transaction; included if and only if `TransactionResult` value is `NotCompleted`.                            |

### 3.4 W2WPresentationRequest

The `W2WPresentationRequest` class is used within the definition of [`Transaction`](#31-transaction) for presentation requests sent out from the Wallet Unit directly to another one in a wallet-to-wallet scenario. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party (requestee) interacting with the verifier's (requestor's) Wallet Unit, where the following values are defined in the present document: <li>`NaturalPerson`</li> (*Note: only `NaturalPerson` type applies to wallet-to-wallet transactions.*)             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name is `NaturalPerson.givenName` + `NaturalPerson.familyName`, as specified in [Provider information specification].    |
| `typeOfData`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the data requested, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `listOfClaimsRequested`      | [1..*]       | Array of [*ClaimInfo*](#3202-claiminfo) objects      | contains an array with information about all the claims (User-related attributes) requested from another User's Wallet Unit in the request of the verifying party.     |
| `listOfClaimsPresented`      | [1..*]       | Array of [*ClaimInfo*](#3202-claiminfo) objects      | contains an array with information about all the claims (User-related attributes) presented to the User's Wallet Unit being a verifier (requestor).|

### 3.5 W2WPresentation

The `W2WPresentation` class is used within the definition of [`Transaction`](#31-transaction) for presentation requests processed by the Wallet Unit (including pseudonym transactions) received directly from another Wallet Unit in a wallet-to-wallet scenario. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party (requestor) interacting with a Wallet Unit, where the following values are defined in the present document: <li>`NaturalPerson`</li> (*Note: only `NaturalPerson` type applies to wallet-to-wallet transactions.*)             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name is `NaturalPerson.givenName` + `NaturalPerson.familyName`, as specified in [Provider information specification].    |
| `typeOfData`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the data requested, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `listOfClaimsRequested`      | [1..*]       | Array of [*ClaimInfo*](#3202-claiminfo) objects      | contains an array with information about all the claims (User-related attributes) requested by another User's Wallet Unit in its request.     |
| `listOfClaimsPresented`      | [1..*]       | Array of [*ClaimInfo*](#3202-claiminfo) objects      | contains an array with information about all the claims (User-related attributes) presented to the User's Wallet Unit being a verifier (requestor).|
| `ReasonOfNoncompletion`    | [0..1]       | *string*      |       specifies the **reason of non completion** of a transaction; included if and only if `TransactionResult` value is `NotCompleted`.                            |

### 3.6 CredentialIssuance

The `CredentialIssuance` class is used within the definition of [`Transaction`](#31-transaction) and specifies the attributes related to credential (PID and attestation) issuance transactions. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [1..1]       | *[Identifier](#32011-identifier-external)*       |   specifies one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.       |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in the credential issuance transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12): <li>`QEAAProvider`</li><li>`NonQEAAProvider`</li><li>`PubEEAProvider`</li><li>`PIDProvider`</li>             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the issuer of the credentials in the issuance transaction. The issuer's name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification]. (*Note: In some Member States the name of the legal person may be given in more than one language, which necessitates use of *[MultiLangString](#32012-multilangstring-external)*  type of the class*).   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following items: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification]. If the interacting party is a legal person, then the `LegalEntity.postalAddress` attribute is not present.    |  
| `+ credentialTypeRequested`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the credentials type requested for issuance, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `+ credentialNumberRequested`      | [1..1]       | *integer*      |      contains the total number of the credentials requested for issuance.   |
| `+ credentialTypeIssued`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the credentials issued to the Wallet Unit in the transaction, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `+ credentialNumberIssued`      | [1..1]       | *integer*      |      contains the total number of the credentials issued in the transaction.   |
| `credentialIdentifier`      | [1..*]       | Array of *string* objects       |    contains an array of **identifiers of the credentials** issued to a Wallet Unit in the transaction.   |
| `isUserTriggered`    | [1..1]       | *boolean*      |       indicates whether the issuance transaction is triggered buy the Wallet Unit User ('TRUE') or by the credential issuer ('FALSE').                            |

### 3.7 CredentialDeletion

The `CredentialDeletion` class is used within the definition of [`Transaction`](#31-transaction) for credential (PID or Attestation) deletion transactions processed by the Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `credentialIdentifier`      | [1..1]       | *string*       |    contains the **identifier of the credential** deleted by the User from the Wallet Unit in the deletion transaction.   |
| `typeOfData`      | [1..1]       | *string*      |      contains the **type** of the deleted credential, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `credentialIssuer`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the deleted credential's issuer name. The issuer's name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification].   |

### 3.8 PseudonymGeneration

The `PseudonymGeneration` class is used within the definition of [`Transaction`](#31-transaction) for (User's) pseudonym generation  transactions. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `pseudonym`      | [1..1]       | *[Pseudonym](#3204-pseudonym)*       |    specifies the attributes of the pseudonym of the User.   |

### 3.9 PseudonymDeletion

The `PseudonymDeletion` class is used within the definition of [`Transaction`](#31-transaction) for (User's) pseudonym deletion  transactions. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `pseudonym`      | [1..1]       | *[Pseudonym](#3204-pseudonym)*       |    specifies the attributes of the pseudonym of the User.   |

### 3.10 CertificateIssuance

The `CertificateIssuance` class is used within the definition of [`Transaction`](#31-transaction) for signing or sealing certificate  issuance and/or associated key pair generation. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [1..1]       | *[Identifier](#32011-identifier-external)*     |   specifies one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.       |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in the certificate issuance transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12): <li>`QCertForESealProvider`</li><li>`QCertForESigProvider`</li>             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification].   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following items: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification]. If the interacting party is a legal person, then the `LegalEntity.postalAddress` attribute is not present.    |  
| `certificateIdentifier`      | [1..1]       | *string*      |     contains **identifier of the certificate** issued to the Wallet Unit in the issuance transaction.   |

### 3.11 CertificateDeletion

The `CertificateDeletion` class is used within the definition of [`Transaction`](#31-transaction) for signing or sealing certificate deletion transactions. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `certificateIdentifier`      | [1..1]       | *string*      |     contains **identifier of the certificate** that was deleted in the transaction.   |
| `certificateIssuerIdentifier`       | [1..1]       | *[Identifier](#32011-identifier-external)*     |   specifies one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.       |
| `certificateIssuerName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification].   |

### 3.12 SigningSealing

The `SigningSealing` class is used within the definition of the [`Transaction`](#31-transaction) for signing or sealing transactions processed by the Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [1..1]       | *[Identifier](#32011-identifier-external)*      |   specifies one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.      |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a signing or sealing transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration]): <li>`ESigESealCreationProvider`</li>              |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification].   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following items: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification]. If the interacting party is a legal person, then the `LegalEntity.postalAddress` attribute is not present.     |  
| `signingTransactionIdentifier`      | [1..1]       | *string*      |     specifies the **identifier of the signing or sealing** transaction processed by the Wallet Unit.   |
| `certificateIdentifier`      | [1..1]       | *string*      |     contains **identifier of the certificate** used in the signing or sealing transaction processed by the Wallet Unit.    |
| `dtbsr`      | [1..1]       | *string*      |     contains **data to be signed representation** (DTBS/R) used in the signing or sealing transaction processed by the Wallet Unit.    |
| `fileIdentifier`      | [0..1]       | *string*      |     may contain the **identifier of the signed or sealed file**  in the signing or sealing transaction processed by the Wallet Unit, associated with `dtbsr`.    |
| `fileName`      | [0..1]       | *string*      |     may contain the **name of the signed or sealed file**  in the signing or sealing transaction processed by the Wallet Unit, associated with `dtbsr`.     |
| `fileSize`      | [0..1]       | *string*      |     may contain the **size of the signed or sealed file** signed or sealed in the signing or sealing transaction processed by the Wallet Unit, associated with `dtbsr`.     |

### 3.13 DataDeletionRequest

The `DataDeletionRequest` class is used within the definition of the [`Transaction`](#31-transaction) for sending (or sending process initiation) of the User's data deletion requests to a Wallet-Relying Party (that previously requested a presentation and obtained User's data) from the Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [1..1]       | *[Identifier](#32011-identifier-external)*      |   specifies one or more identifiers of the legal entity (being a Wallet-Relying Party), as stated in an official record together with the identification data of that official record, if applicable.      |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the interacting party, being a Wallet-Relying Party and a subject of the data deletion request. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification].   |
| `listOfClaims`      | [1..*]       | Array of [*ClaimInfo*](#3202-claiminfo) objects      | contains an array with information about all the claims (User-related attributes) to be deleted by the interacting party (being a Wallet-Relying Party).    |

### 3.14 DPAReport

The `DPAReport` class is used within the definition of the [`Transaction`](#31-transaction) for sending (or sending process initiation)  of the report to a data protection authority by the Wallet Unit's User from the Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `dpaName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     specifies **name** of the data protection authority to which the report was sent.   |
| `dpaCountry`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     specifies **country** of the data protection authority to which the report was sent.   |
| `report`      | [1..*]       | *string*      |       contains the content (text) of the report.     |

### 3.15 OtherTransaction

The `OtherTransaction` class is used within the definition of the [`Transaction`](#31-transaction) for any other transactions processed by the Wallet Unit, not specified earlier in the present document (for instance User's data backup or the migration object export). It contains attributes specified in the following table, as well as it may contain additional, proprietary attributes, as specified by the Wallet Solution provider (out of this document).

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `description`      | [1..*]       | *string*      |       contains the human-readable **description** of the transaction such as type, nature, context etc. The content and syntax are at the full discretion of the Wallet Solution provider.    |

### 3.16 TransactionLog

The `TransactionLog` object is a data file containing `Transaction` objects.

### 3.17 TransactionLogObject

The `TransactionLogObject` object is a container that contains the `TransactionLog` object (an exportable file). The `TransactionLogObject` object shall be in [JWE] format; the corresponding [JWE] ciphertext object shall be encoded as a [JSON] object.
The encryption process and recommended algorithms for [JWE] encryption are defined in Section 4. 

### 3.18 ListOfCredentials

The `ListOfCredentials` object is a data file containing one or more `Credential` objects.

### 3.19 MigrationObject

The `MigrationObject` object is a container (an exportable file), that contains two files: `TransactionLog` and `ListOfCredential`. The `MigrationObject` object shall be in [JWE] format; the corresponding [JWE] ciphertext is composed of the `TransactionLog` and `ListOfCredential` objects, both encoded as [JSON] objects.
The encryption process and recommended algorithms for [JWE] encryption are defined in Section 4. 

### 3.20 Auxiliary classes

#### 3.20.1 CredentialInfo

The `CredentialInfo` class represents the set of data related to a credential, that is necessary to create `ListOfCredentials` in the `MigrationObject` object. It inherits all attributes from the (external) `Credential` class. In addition it contains the attributes specified in the following table:

| Attribute                 | Multiplicity | Type | Description |
|---------------------------|--------------|------|-------------|
| `credentialIdentifier`    | [1..1]       | *string*     | contains the **identifier of the credential**. (*Note: the specification of this attribute is not yet established and is the subject of further works. It may be specified in the related Attestation Rulebook.*) |
| `typeOfData`      | [1..1]       | *string*      |      contains the **type** of the credential (attestation), where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `credential`            | [1..1]       | *[Credential](#3205-credential-external)*              |  specifies attributes of the **credential** (attestation).  |
| `issuanceTime`            | [1..1]       | *string*              |  contains the **date and time** of the credential issuance in ISO 8601 format (YYYY-MM-DDTHH:mm:ss).  |
| `issuerIdentifier`       | [1..*]       | *[Identifier](#32011-identifier-external)*      |   specifies one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.       |
| `issuerType`       | [1..1]       | *string*      |          specifies the **type** of the issuer, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12, plus additional ones): <li>`QEAAProvider`</li><li>`NonQEAAProvider`</li><li>`PubEEAProvider`</li><li>`PIDProvider`</li>             |
| `issuerName`      | [1..1]       | *[MultiLangString](#32012-multilangstring-external)*      |     contains the **name** of the issuer. The issuer name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>`NaturalPerson.givenName` + `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification].    |

#### 3.20.2 ClaimInfo

The `ClaimInfo` class represents the set of data related to a claim (a User-related attribute). It inherits all attributes from the (external) `Claim` class. In addition it contains the attributes specified in the following table:

| Attribute                 | Multiplicity | Type | Description |
|---------------------------|--------------|------|-------------|
| `claimIdentifier`    | [1..1]       | *string*     | contains  an **identifier of the claim**. |
| `claim`    | [1..*]       | Array of *[Claim](#3196-claim-external)* objects     | contains a non-empty array of objects that specifies attributes in a credential (attestation). See [OID4VP] Section 6.3. |

#### 3.20.3 ListOfClaims

The `ListOfClaims` class represents the list of claims object. It contains one or more `ClaimInfo` objects.

#### 3.20.4 Pseudonym

| Attribute                 | Multiplicity | Type | Description |
|---------------------------|--------------|------|-------------|
| `value`      | [1..1]       | *string*       |    contains the **value** of the pseudonym, which is a public key of the asymmetric key pair generated by the Wallet Unit for this purpose.    |
| `alias`      | [0..1]       | *string*       |    may contain the user-friendly **alias**  of the pseudonym, set by the User. This attribute is optional.    |

#### 3.20.5 Credential (external)

The `Credential` class contains the attributes specified in [Relying Party registration specification].

#### 3.20.6 Claim (external)

The `Claim` class contains the attributes specified in [Relying Party registration specification], except `value`.

#### 3.20.7 LegalEntity (external)

The `LegalEntity` class contains the attributes specified in [Provider information specification].

#### 3.20.8 LegalPerson (external)

The `LegalPerson` class is used within the definition of the LegalEntity class and allows to specify the attributes of a legal person. It contains the attributes specified in the [Provider information specification].

#### 3.20.9 NaturalPerson (external)

The `NaturalPerson` class is used within the definition of the LegalEntity class and allows to specify the attributes of a legal person. It contains the attributes specified in the [Provider information specification].

#### 3.20.10 IntendedUse (external)

The `IntendedUse` class contains the attributes specified in the [Relying Party registration specification].

#### 3.20.11 Identifier (external)

The `Identifier` class contains the attributes specified in [Provider information specification].

#### 3.20.12 MultiLangString (external)

The `MultiLangString` class contains the attributes specified in the [Relying Party registration specification].

#### 3.20.13 Policy (external)

The `Policy` class contains the attributes specified in the [Provider information specification].

### 4 Encryption

The following algorithms are recommended for [JWE] objects encryption:
* [PBES2-HS256+A128KW](https://www.rfc-editor.org/rfc/rfc7518.html#section-4.8), as specified in [JWA] and [PKCS#5] - to derive the key encryption key from the user password/passphrase and encrypt the content encryption key,
* [A128GCM](https://www.rfc-editor.org/rfc/rfc7518.html#section-5.3), as specified in [JWA] - for the content encryption with the content encryption key.

The encryption process is visualised in the diagram below: 

![EncryptionDiagram](img/TS10-encryption.svg)

### 5 References

| Reference      | Description |
|----------------|-------------|
| [Regulation (EU) No 910/2014] | [Regulation (EU) No 910/2014 of the European Parliament and of the Council of 23 July 2014 on electronic identification and trust services for electronic transactions in the internal market and repealing Directive 1999/93/EC](https://eur-lex.europa.eu/legal-content/EN/TXT/HTML/?uri=CELEX:32014R0910) |
| [Regulation (EU) 2024/1183] | [Regulation (EU) No 910/2014 of the European Parliament and of the Council of of 11 April 2024 amending Regulation (EU) No 910/2014 as regards establishing the European Digital Identity Framework](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183)  |
| [CIR on Integrity and Core Functionalities]  | [Commission Implementing Regulation (CIR) EU 2024/2979 of 28 November 2024 laying down rules for the application of Regulation (EU) No 910/2014 of the European Parliament and of the Council as regards the integrity and core functionalities of European Digital Identity Wallets](https://eur-lex.europa.eu/eli/reg_impl/2024/2979/oj/eng)
| [CIR for Relying Party Registration]      | [Commission Implementing Regulation (EU) 2025/848 of 6 May 2025 laying down rules for the application of Regulation (EU) No 910/2014 of the European Parliament and of the Council as regards the registration of wallet-relying parties](http://data.europa.eu/eli/reg_impl/2025/848/oj)  |
| [Provider information specification]      | [Specification of systems enabling the notification and subsequent publication of Provider information](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts2-notification-publication-provider-information.md) |
| [Relying Party registration specification]      | [Specification of common formats and API for Relying Party Registration information](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md) |
| [ARF]      | [Architecture and Reference Framework, version 2.3](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/architecture-and-reference-framework-main.md) |
| [PKCS#12]      | [PKCS #12: Personal Information Exchange Syntax v1.1, RFC 7292](https://datatracker.ietf.org/doc/rfc7292/) |
| [JWE]      | [JSON Web Encryption (JWE), RFC 7516](https://datatracker.ietf.org/doc/rfc7516/) |
| [COSE_Encrypt]      | [CBOR Object Signing and Encryption (COSE): Structures and Process, RFC 9052](https://datatracker.ietf.org/doc/rfc9052/) |
| [PKCS#5]      | [PKCS #5: Password-Based Cryptography Specification Version 2.1, RFC 8018](https://datatracker.ietf.org/doc/rfc8018/) |
| [JWA]      | [JSON Web Algorithms (JWA), RFC 8018](https://datatracker.ietf.org/doc/rfc7518/) |
| [JSON]      | [The JavaScript Object Notation (JSON) Data Interchange Format, RFC 8259](https://datatracker.ietf.org/doc/html/rfc8259) |
| [ECCG]      | [ECCG Agreed Cryptographic Mechanisms - version 2](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/issues/416) |
