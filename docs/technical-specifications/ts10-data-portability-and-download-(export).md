
<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Data Portability and Download (Export)

## Abstract

The present document specifies the common format and data set for the transaction log and the Migration Object, as well as protocols related to exporting those objects from a Wallet Unit and importing to another one in a backup, recovery and migration scenarios, as required by the [European Digital Identity Regulation (EU 2024/1183)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183) and [CIR 2024/2979](https://eur-lex.europa.eu/eli/reg_impl/2024/2979/oj/eng).

### [GitHub discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/407)

## Versioning

| Version | Date        | Description                              |
|---------|-------------|------------------------------------------|
| `0.1`   | 27.06.2025  | Initial version for the first discussion |

# 1. Introduction

## 1.1 Overview

According to [**European Digital Identity Regulation (EU 2024/1183)**](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183) - hereinafter referred as the [Regulation] - and the Commission Implementing Regulation (CIR) [**EU 2024/2979**](https://eur-lex.europa.eu/eli/reg_impl/2024/2979/oj/eng) of 28 November 2024 laying down rules for the application of Regulation (EU) No 910/2014 of the European Parliament and of the Council as regards the integrity and core functionalities of European Digital Identity Wallets - hereinafter referred as the [CIR on Integrity and Core Functionalities] - the Wallet Units shall keep a transaction log, as well as a "migration object" that can be used to export User's data from a Wallet Unit and to import it to another Wallet Unit, in backup, recovery and migration processes.
The relevant requirements are set out in Article 5a(4)(f) and (g) of the [Regulation] and Article 9 and 13 of the [CIR on Integrity and Core Functionalities].

## 1.2 Scope

The present document specifies:

- the common format and content for the transaction log,
- the common format and content for the migration object,
- security measures, protocols and algorithms utilised in backup, recovery and migration processes (*tbc*).

The migration object contains:

- a file containig the list of credentials (PIDs and Atestations) present in a Wallet Unit,
- a transaction log file.

## 1.3 Requirements Notation

The key words "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC2119 RFC8174 when, and only when, they are written in all capital letters.

# 2. Data Model

The present data model has been created based on the existing implementing act CIR [(EU) 2024/2980](https://eur-lex.europa.eu/eli/reg_impl/2024/2980/oj) and
and the published drafts of the forthcoming implementing acts [Draft of the CIR for PuB-EAA](https://tinyurl.com/IA-45def-draft) and [Draft of the CIR for RP-Registration](https://tinyurl.com/IA-5b-draft),
while keeping the [SEMIC Style Guide](https://semiceu.github.io/style-guide/1.0.0/index.html) in mind and aligning it with the [Core Business Vocabulary](https://semiceu.github.io/Core-Business-Vocabulary/releases/2.2.0/) as much
as reasonable.

![MigrationObjectAndTransactionLogDataModel](img/ts10-eudiw-mig-obj-trans-log-datamodel.svg)

As outlined in the above figure, it contains the following **main classes**:

- **Transaction**
- **Presentation**
- **CredentialIssuance**
- **CertificateIssuance**
- **SigningSealing**
- **CredentialDeletion**
- **W2WPresentationRequest**
- **TransactionDeletion**
- **DPAClaim**
- **OtherTransaction**
- **TransactionLog**
- **ListOfCredentials**
- **MigrationObject**

These classes are defined using the following **auxiliary classes**:

- **CredentialInfo**
- **ClaimInfo**
- **CredentialIssuer**
- **TransactionIdentifier**

These classes depend on the following auxiliary classes of the [Relying Party registration specification] and the [Provider information specification]:

- **Credential**
- **Claim**
- **LegalEntity**
- **LegalPerson**
- **NaturalPerson**
- **IntendedUse**
- **Identifier**
- **MultiLangString**

# 3 Specification

## 3.1 Transaction

The `Transaction` class is the central class of the transaction log data model and it assembles the generic attributes related to a transaction performed with or by a Wallet Unit, relevant for the transaction log creation. It contains the attributes specified in the following table:

| Attribute          | Multiplicity | Type  | Description  |
|--------------------|--------------|-------|--------------|
| `presentation`      | [1..1]       | [*Presentation*](#32-presentation)      | specifies the specific attributes of a **presentation** transaction. This attribute is present, if and only if the transaction is a presentation transaction.        |
| `credentialIssuance`      | [1..1]       | [*CredentialIssuance*](#33-credentialissuance)      | specifies the specific attributes of a **credential (attestation) issuance** transaction. This attribute is present, if and only if the transaction is credential issuance.        |
| `certificatelIssuance`      | [1..1]       | [*CertificateIssuance*](#34-certificateissuance)      | specifies the specific attributes of a **certificate issuance** transaction. The certificate can be for signing or sealing. This attribute is present, if and only if the transaction is certificate issuance.        |
| `signingSealing`      | [1..1]       | [*SigningSealing*](#35-signingsealing)      | specifies the specific attributes of a **signing or sealing** transaction.  This attribute is present, if and only if the transaction is signature or seal creation transaction.        |
| `credentialDeletion`      | [1..1]       | [*CredentialDeletion*](#36-credentialdeletion)      | specifies the specific attributes of a **credential deletion**.  This attribute is present, if and only if the transaction is deletion of a credential from the Wallet Unit.        |
| `w2wPresentation`      | [1..1]       | [*W2WPresentation*](#37-w2wpresentation)      | specifies the specific attributes of a **presentation** transaction performed by the Wallet Unit upon request received from another Wallet Unit, in the wallet-to-wallet interaction scenario.  This attribute is present, if and only if the transaction is a presentation transaction in wallet-to-wallet mode.        |
| `w2wPresentationRequest`      | [1..1]       | [*W2WPresentationRequest*](#38-w2wpresentationrequest)      | specifies the specific attributes of a **presentation request** sending (in-bound) transaction.  This attribute is present, if and only if the transaction is sending a presentation request and logged in the transaction log of the requestors Wallet Unit.        |
| `transactionDeletion`      | [1..1]       | [*TransactionDeletion*](#39-transactiondeletion)      | specifies the specific attributes of **transaction deletion**.  This attribute is present, if and only if the transaction is about deletion of an tramsacttion log entry (entries) from the transaction log.        |
| `dpaClaim`      | [1..1]       | [*DPAClaim*](#310-dpaclaim)      | specifies the specific attributes of a **data protection authority claim** transaction.  This attribute is present, if and only if the transaction is sending a claim to a data protection authority by the Wallet Unit User directly from the Wallet Unit.        |
| `otherTransaction`      | [1..1]       | [*OtherTransaction*](#311-othertransaction)      | specifies the specific attributes of **other** types of transaction logged in the transaction log.  This attribute is present, if and only if the transaction non of the above mentioned transaction types.        |
| `transactionIdentifier`      | [1..1]       | [*TransactionIdentifier*](#3164-transactionidentifier)      | specifies the specific attributes of a **transaction identifier**.        |
| `Time`      | [1..1]       | *string*      |           contains the **date and time** of the transaction. (*EDITOR'S NOTE: to add the format specification or class reference*)              |
| `transactionType`      | [1..1]       | *string*     |           contains the **type** of the transaction, where the following values are defined  in the present document: <ul><li>`Presentation`</li><li>`CredentialIssuance`</li><li>`CertificateIssuance`</li><li>`SigningSealing`</li><li>`CredentialDeletion`</li><li>`W2WPresentation`</li><li>`W2WPresentationRequest`</li><li>`TransactionDeletion`</li><li>`DPAClaim`</li><li>`OtherTransaction`</li></ul>         |
| `transactionResult`     | [1..1]       | *string*      |         specifies the **result** of the transaction, where the following values are defined in the present document: <li>`Completed`</li><li>`NotCompleted`</li>                                                               |
| `transactionDetail`     | [1..1]       | [*TransactionDetail*](#tbd)      |         specifies the remaining **details** of the transationdepending on the transaction type as defined by `transactionType`.                                                |

## 3.2 Presentation

The `Presentation` class is used within the definition of the [`Transaction`](#31-transaction) and specifies the attributes related to the presentation requests received and processed by a Wallet Unit (including pseudonym transactions). It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [0..1]       | `Identifier` as specified in [Provider information specification]      |   may be present in order to specify one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.  Not present if `interactingPartyType` is natural person in a wallet to wallet interaction scenario.      |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12, plus additional ones): <li>`ServiceProvider`</li><li>`QEAAProvider`</li><li>`NonQEAAProvider`</li><li>`PubEEAProvider`</li><li>`PIDProvider`</li><li>`QCertForESealProvider`</li><li>`QCertForESigProvider`</li><li>`rQSigCDProvider`</li><li>`rQSealCDProvider`</li><li>`ESigESealCreationProvider`</li><li>`NaturalPerson`</li> (*Note: only `NaturalPerson` type applies to wallet-to-wallet transactions.*)             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#31611-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>concatenation `NaturalPerson.givenName` and `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification]. (*Note: In some cases the name of the legal person is given in more then one language, which nessesitates use of *[MultiLangString](#31611-multilangstring-external)*  type of the class*).   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following items: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification].     |  
| `purpose`       | [1..*]       |   [*MultiLangString*](#31611-multilangstring-external)    |        specifies one or more purposes of the intended data processing according to Article 5 1. (b) of (EU) 2016/679 as `IntendedUse.purpose` attribute specified in [Provider information specification].                   |
| `privacyPolicy`    | [1..*]       | [*Policy*](#tbd) as defined in [Provider information specification] |     specifies one or more purposes of the intended data processing according to Article 5 1. (b) of (EU) 2016/679, as equals to `IntendedUse.privacyPolicy` attribute specified in [Provider information specification].            |
| `typeOfData`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the data involved in a transaction, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `credentialIdentifier`      | [0..*]       | [*credentialIdentifier*](#tbd)       |     specifies **identifier of the credential** deleted by the User from the Wallet Unit's.   |
| `listOfClaims`      | [1..*]       | Array of [*ClaimInfo*](#3162-claiminfo) objects      | conains an array with information about all the claims (Wallet Unit User's related attributes) involved in a transaction.     |
| `ReasonOfNoncompletion`    | [0..1]       | *string*      |       specifies the **reason of non completion** of a transaction; included if and only if `TransactionResult` value is `NotCompleted`.                            |

## 3.3 CredentialIssuance

The `CredentialIssuance` class is used within the definition of the [`Transaction`](#31-transaction) and specifies the attributes related to credential (PID and attestation) issuance transactions. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [0..1]       | `Identifier` as specified in [Provider information specification]      |   may be present in order to specify one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.  Not present if `interactingPartyType` is natural person in a wallet to wallet interaction scenario.      |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12): <li>`QEAAProvider`</li><li>`NonQEAAProvider`</li><li>`PubEEAProvider`</li><li>`PIDProvider`</li>             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#31611-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>concatenation `NaturalPerson.givenName` and `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification]. (*Note: In some cases the name of the legal person is given in more then one language, which nessesitates use of *[MultiLangString](#31611-multilangstring-external)*  type of the class*).   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following items: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification].     |  
| `credentialIdentifier`      | [1..1]       | Array of [*credentialIdentifier*](#tbd) objects       |     specifies **identifier of the credential** issued by the interacting party (`QEAAProvider`,`NonQEAAProvider`, `PubEEAProvider` or `PIDProvider`) to a Wallet Unit. [tbd]   |

## 3.4 CertificateIssuance

The `CertificateIssuance` class is used within the definition of the [`Transaction`](#31-transaction) for signing and sealing certificate issuance transactions. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [0..1]       | `Identifier` as specified in [Provider information specification]      |   may be present in order to specify one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.  Not present if `interactingPartyType` is natural person in a wallet to wallet interaction scenario.      |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12): <li>`QCertForESealProvider`</li><li>`QCertForESigProvider`</li>             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#31611-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>concatenation `NaturalPerson.givenName` and `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification]. (*Note: In some cases the name of the legal person is given in more then one language, which nessesitates use of *[MultiLangString](#31611-multilangstring-external)*  type of the class*).   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following items: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification].     |  
| `certificateIdentifier`      | [1..1]       | [*certificateIdentifier*](tbd)      |     specifies **identifier of the certificate** issued by the interacting party (`QCertForESealProvider` or `QCertForESigProvider`) to a Wallet Unit. [tbd]   |

## 3.5 SigningSealing

The `SigningSealing` class is used within the definition of the [`Transaction`](#31-transaction) for signing or sealing transactions processed by a Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyIdentifier`       | [0..1]       | `Identifier` as specified in [Provider information specification]      |   may be present in order to specify one or more identifiers of the legal entity, as stated in an official record together with the identification data of that official record, if applicable.  Not present if `interactingPartyType` is natural person in a wallet to wallet interaction scenario.      |
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12, plus additional ones): <li>`ServiceProvider`</li><li>`QEAAProvider`</li><li>`NonQEAAProvider`</li><li>`PubEEAProvider`</li><li>`PIDProvider`</li><li>`QCertForESealProvider`</li><li>`QCertForESigProvider`</li><li>`rQSigCDProvider`</li><li>`rQSealCDProvider`</li><li>`ESigESealCreationProvider`</li><li>`NaturalPerson`</li> (*Note: `NaturalPerson` type applies to wallet-to-wallet transactions.*)             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#31611-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name may be: <li>`LegalPerson.name` for legal person type of legal entity, or </li><li>concatenation `NaturalPerson.givenName` and `NaturalPerson.familyName` for the natural person </li> as specified in [Provider information specification]. (*Note: In some cases the name of the legal person is given in more then one language, which nessesitates use of *[MultiLangString](#31611-multilangstring-external)*  type of the class*).   |
| `interactingPartyContact`      | [1..1]  |  Array of *string* objects      | contains an array with the following items: <li>`LegalEntity.identifier`</li><li>`LegalEntity.postalAddress`</li><li>`LegalEntity.country`</li><li>`LegalEntity.email`</li><li>`LegalEntity.phone`</li><li>`LegalEntity.infoURI`</li> as specified in [Provider information specification].     |  
| `signingTransactionIdentifier`      | [1..1]       | [*signingTransactionIdentifier*](#tbd)      |     specifies **identifier of signing or sealing** transaction processed by a Wallet Unit. (*Note: this identifier is provided by the interacting party, for instance via transactional data mechanism.*)   |
| `certificateIdentifier`      | [1..1]       | [*certificateIdentifier*](#tbd)      |     specifies **identifier of the certificate** used in the signing or sealing transaction processed by a Wallet Unit. (*Note: this identifier is provided by the interacting party, for instance via transactional data mechanism.*)   |
| `dtbsr`      | [0..*]       | [*dtbsr*](#tbd)      |     specifies **data to be signed representation** used in the signing or sealing transaction processed by a Wallet Unit. (*Note: this data is provided by the interacting party, for instance via transactional data mechanism.*)   |
| `fileIdentifier`      | [0..*]       | [*fileIdentifier*](#tbd)      |     specifies the **identifier of the file** signed or sealed in the signing or sealing transaction processed by a Wallet Unit, associated with `dtbsr`.  (*Note: this data is provided by the interacting party, for instance via transactional data mechanism.*)   |
| `fileName`      | [0..*]       | [*fileName*](#tbd)      |     specifies the **file name** of the signed or sealed file in the signing or sealing transaction processed by a Wallet Unit, associated with `dtbsr`. (*Note: this data is provided by the interacting party, for instance via transactional data mechanism.*)    |
| `fileSize`      | [0..*]       | [*fileSize*](#tbd)      |     specifies the **size of the file** signed or sealed in the signing or sealing transaction processed by a Wallet Unit, associated with `dtbsr`. (*Note: this data is provided by the interacting party, for instance via transactional data mechanism.*)    |

## 3.6 CredentialDeletion

The `CredentialDeletion` class is used within the definition of the [`Transaction`](#31-transaction) for credential (PID or Attestation) deletion transactions processed by a Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `credentialIdentifier`      | [0..*]       | [*credentialIdentifier*](#tbd)       |     specifies **identifier of the credential** deleted by the User from the Wallet Unit's.   |

## 3.7 W2WPresentation

The `W2WPresentation` class is used within the definition of the [`Transaction`](#31-transaction) for presentation requests processed by a Wallet Unit (including pseudonym transactions) received directly from another Wallet Unit in a wallet-to-wallet scenario. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12, plus additional ones): <li>`NaturalPerson`</li> (*Note: only `NaturalPerson` type applies to wallet-to-wallet transactions.*)             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#31611-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name is defined as concatenation of `NaturalPerson.givenName` and `NaturalPerson.familyName`, as specified in [Provider information specification]. (*Note: In some cases the name of a person is given in more then one language, which nessesitates use of *[MultiLangString](#31611-multilangstring-external)*  type of the class*).   |
| `typeOfData`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the data involved in a transaction, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `listOfClaims`      | [1..1]       | Array of [*Claims*](#tbd) as specified in [Relying Party registration specification]      | conains an array with all the claims (and Wallet Unit User's attributes) involved in a transaction.     |
| `ReasonOfNoncompletion`    | [0..1]       | *string*      |       specifies the **reason of non completion** of a transaction; included if and only if `TransactionResult` value is `NotCompleted`.                            |

## 3.8 W2WPresentationRequest

The `W2WPresentationRequest` class is used within the definition of the [`Transaction`](#31-transaction) for presentation requests sent out from the Wallet Unit directly to another one in a wallet-to-wallet scenario. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `interactingPartyType`       | [1..1]       | *string*      |          specifies the **type** of the party interacting with a Wallet Unit in a transaction, where the following values are defined in the present document (following [CIR for Relying Party Registration] Annex I point 12, plus additional ones): <li>`NaturalPerson`</li> (*Note: only `NaturalPerson` type applies to wallet-to-wallet transactions.*)             |
| `interactingPartyName`      | [1..1]       | *[MultiLangString](#31611-multilangstring-external)*      |     contains the **name** of the party interacting with a Wallet Unit in a transaction. The interacting party name is defined as concatenation of `NaturalPerson.givenName` and `NaturalPerson.familyName`, as specified in [Provider information specification]. (*Note: In some cases the name of a person is given in more then one language, which nessesitates use of *[MultiLangString](#31611-multilangstring-external)*  type of the class*).   |
| `typeOfData`      | [1..*]       | Array of *string* objects      |      contains an array with one or more **types** of the data involved in a transaction, where the following values are defined in the present document: <li>`PID`</li><li>`QEAA`</li><li>`NonQEAA`</li>   |
| `listOfClaims`      | [1..*]       | Array of [*ClaimInfo*](#tbd) objects      | conains an array with information about all the claims (Wallet Unit User's related attributes) involved in a transaction.     |

## 3.9 TransactionDeletion

The `TransactionDeletion` class is used within the definition of the [`Transaction`](#31-transaction) for a User action of transaction entry (entries) deletion from the transaction log in the Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `transactionIdentifier`      | [1..1]       | [*transactionIdentifier*](#3164-transactionidentifier)      | specifies the specific attributes of a **transaction identifier**. (*Note: here it refers to the transaction identifier of the deleted transaction entry from the transaction log.*)        |

## 3.10 DPAClaim

The `DPAClaim` class is used within the definition of the [`Transaction`](#31-transaction) for claims sent to a data protection autority from a Wallet Unit. It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `dpaName`      | [1..1]       | *[MultiLangString](#31611-multilangstring-external)*      |     specifies **name** of the data protection authority to which the claim was sent from the Wallet Unit.   |
| `claimContent`      | [1..*]       | *string*      |       contains the full text of the claim (*EDITOR'S NOTE: to add a specification.*)     |

## 3.11 OtherTransaction

The `OtherTransaction` class is used within the definition of the [`Transaction`](#31-transaction) for any other transactions processed by a Wallet Unit, not specified earlier in this section of the present document (for instance User's data backup). It contains attributes specified in the following table:

| Attribute          | Multiplicity | Type | Description |
|--------------------|--------------|------|-------------|
| `otherTransactionInformation`      | [1..*]       | *string*      |       contains the proprietary information about a non-specified transaction type.    |

## 3.12 TransactionLog

The `TransactionLog` object is a data file in JSON format containing `Transaction` objects.

## 3.14 ListOfCredentials

The `ListOfCredential` object is a data file in JSON format containing `Credential` objects.

## 3.15 MigrationObject

The `MigrationObject` object is a container that contains two files: `TransactionLog` and `ListOfCredential`. The format of the `MigrationObject` is pkcs#12.

## 3.16 Auxiliary classes

### 3.16.1 CredentialInfo

The `CredentialInfo` class represents the set of data related to a credential, that is necessary to create `ListOfCredentials` in the `MigrationObject` object. It inherits all attributes from the (external) `Credential` class. In addition it contains the attributes specified in the following table:

| Attribute                 | Multiplicity | Type | Description |
|---------------------------|--------------|------|-------------|
| `credentialIdentifier`    | [1..1]       | *string*     | contains a unique **identifier of the credential**. (*Note: the specification of this attribute is not yet established and is the subject of further works. It may be specified in the related Attestation Rulebook.*) |
| `credentialType`          | [1..1]       | *string*              |  contains type of the credential. (tbd)                                    |
| `credentialIssuer`        | [1..1]       | [*CredentialIssuer*](#3163-credentialissuer)  |  specifies the **credential issuer**.            |
| `issuanceTime`            | [1..1]       | *string*              |  contains the **date and time** of the issuance of the credential.  |

### 3.16.2 ClaimInfo

The `ClaimInfo` class represents the set of data related to a claim (a User's related attribute in a PID or Attestation). It inherits all attributes from the (external) `Claim` class. In addition it contains the attributes specified in the following table:

| Attribute                 | Multiplicity | Type | Description |
|---------------------------|--------------|------|-------------|
| `claimIdentifier`    | [1..1]       | [*ClaimIdentifier*](#tbd)     | specifies the specific attributes of an **identifier of a claim** (*Note: this is under analysis, to be further specified.*) |

### 3.16.3 CredentialIssuer

(*tbd*)

### 3.16.4 TransactionIdentifier

(*tbd*)

### 3.16.4 Credential (external)

The `Credential` class contains the attributes specified in [Relying Party registration specification].

### 3.16.5 Claim (external)

The `ClaimInfo` class contains the attributes specified in [Relying Party registration specification].

### 3.16.6 LegalEntity (external)

The `LegalEntity` class contains the attributes specified in [Provider information specification].

### 3.16.7 LegalPerson (external)

The `LegalPerson` class is used within the definition of the LegalEntity class and allows to specify the attributes of a legal person. It contains the attributes specified in the [Provider information specification].

### 3.16.8 NaturalPerson (external)

The `NaturalPerson` class is used within the definition of the LegalEntity class and allows to specify the attributes of a legal person. It contains the attributes specified in the [Provider information specification].

### 3.16.9 IntendedUse (external)

The `IntendedUse` class contains the attributes specified in the [Relying Party registration specification].

### 3.16.10 Identifier (external)

The `Identifier` class contains the attributes specified in [Provider information specification].

### 3.16.11 MultiLangString (external)

The `MultiLangString` class contains the attributes specified in the [Relying Party registration specification].

## 4. References

| Reference      | Description |
|----------------|-------------|
| [Regulation] | [European Digital Identity Regulation (EU 2024/1183)](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32024R1183)  |
| [CIR on Integrity and Core Functionalities]  | [Commission Implementing Regulation (CIR) EU 2024/2979 of 28 November 2024 laying down rules for the application of Regulation (EU) No 910/2014 of the European Parliament and of the Council as regards the integrity and core functionalities of European Digital Identity Wallets](https://eur-lex.europa.eu/eli/reg_impl/2024/2979/oj/eng)
| [CIR for Relying Party Registration]      | [Commission Implementing Regulation (EU) 2025/848 of 6 May 2025 laying down rules for the application of Regulation (EU) No 910/2014 of the European Parliament and of the Council as regards the registration of wallet-relying parties](http://data.europa.eu/eli/reg_impl/2025/848/oj)  |
| [Provider information specification]      | [Specification of systems enabling the notification and subsequent publication of Provider information](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts2-notification-publication-provider-information.md) |
| [Relying Party registration specification]      | [Specification of common formats and API for Relying Party Registration information](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md) |
