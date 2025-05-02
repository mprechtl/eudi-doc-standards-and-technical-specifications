<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Specification of common formats and API for Relying Party Registration information

## Abstract

The present document specifies the data formats and application programming interface (API) for the machine-readable Relying Party Registration required by the [European Digital Identity Regulation (EU 910/2014)](https://eur-lex.europa.eu/eli/reg/2014/910/oj/eng).

### [GitHub discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/357)

## Versioning

| Version | Date        | Description                                               |
|---------|-------------|-----------------------------------------------------------|
| `0.1`   | 25.04.2025  | Initial version for first discussion                      |
| `0.2`   | 30.04.2025  | Initial version - fixed data model figure                 |

## 1. Introduction and Overview

The present document specifies the common data formats and the application programming interface (API) related to **Relying Party Registration** according to [**European Digital Identity Regulation (EU 910/2014)**](https://eur-lex.europa.eu/eli/reg/2014/910/oj/eng) ("[Regulation]"). and the Commission Implementing Regulation
(CIR) [**EU 2025/TBA**](https://eur-lex.europa.eu/eli/reg_impl/2025/TBA/oj) of 21 May 2025 on laying down rules for the application of Regulation (EU) No 910/2014 of the European Parliament and of the Council as regards the registration of wallet-relying parties ("[CIR for Relying Party Registration]"). 

Relying Party Registration is addressed in Article 5b (11) of the Regulation and the [CIR for Relying Party Registration] lays down further details, which are in scope of present technical document. 

Specifically on [**CIR for Relying Party Registration**]: 

In the recitals, recital (3) states: 
*To ensure broad access to the registers and to achieve interoperability, Member States should set up both human and machine-readable interfaces that meet the technical specifications set out in this Regulation. Providers of wallet-relying party access certificates and wallet-relying party registration certificates, where available, should, for the purpose of issuing those certificates, also be able to rely upon these interfaces.* 

For the purposes of defining the technical specification, the [CIR for Relying Party Registration] sets specific ruling for:
1. The minimum common data set: The registration of a Relying Party to a registry shall include at least the information set out in Annex I of the [CIR for Relying Party Registration] - see Art. 3(2) and the technical specification [Common Set of Relying Party Information to be Registered](**TS6 public link TBA**)
2. Online access to the registry information: The data shall be made available online both in human-readable form and in a form suitable for automated processing - see Art. 3(4).
3. Information shall be available through a single common API - see Art. 3(5).
4. Common requirements of [CIR for Relying Party Registration] Annex II Section 2 shall be complied with - see Art 3(6).
5. Annex II Sections 1 and 2 define the data format, signing and API requirements:
    - a REST API published according to OpenAPI version 3, 
    - serving data out in JSON format,
    - the output JSON data is signed according to IETF 7515 on JSON Web Signatures (JWS),
    - provide open access,
    - provide API methods for **search and request lists of wallet-relying parties** based on following query parameters: official or business registration number, company name, privacy policy URL, entitlements (including sub-entitlements if defined by a Member State), and wallet-relying party reliance on intermediary and by an associated intermediary where applicable.
    - where query results match at least one wallet-relying party, response shall provide statement information of these wallet relying parties, including information registered according to [CIR for Relying Party Registration] Annex I and full wallet-relying party access certificate (and registration certificate, if applicable) histories, but shall exclude physical address of the registration information of [CIR for Relying Party Registration] Annex I (4).

## 2. Data Model

The data model and common format for Relying Party Registration is derived from the [CIR for Relying Party Registration], the [Common Set of Relying Party Information to be Registered](**TS6 public link TBA**) technical specification and the [Provider information specification], 
while keeping the [SEMIC Style Guide](https://semiceu.github.io/style-guide/1.0.0/index.html) in mind and aligning it with the [SEMIC Core Business Vocabulary](https://semiceu.github.io/Core-Business-Vocabulary/releases/2.2.0/) as much as reasonable. 

> NOTE: The data model for Relying Party Registration data depends on the [Provider information specification] data model, to which it carries a few superclass dependencies as referenced in this section.

![WRP-Reg-DataModel](img/ts5-wrpreg-datamodel.svg)

As outlined in the figure, the data model contains the main class **WalletRelyingParty**, which has class dependencies to superclasses **LegalEntity** and **Provider** specified in [Provider information specification]. 

The main class of the data model is **WalletRelyingParty**, which
* contains the attributes listed in Section 2.1
* as a class inherits attributes of **Provider** and **LegalEntity** superclasses from the [Provider information specification].
* defines its own **auxiliary classes**:
  * **Claim**
  * **Credential** 
  * **IntendedUse**
  * **TransactionData**
  * **Intermediary**

* depends on following auxiliary classes of the [Provider information specification]:
  * 
  * **Identifier**
  * **Law**
  * **Policy**
  * **LegalPerson**


### 2.1 WalletRelyingParty

The ``WalletRelyingParty`` main class inherits all attributes from the ``Provider``
superclass specified in the [Provider information specification]. In addition to the attributes of the ``Provider`` class it contains the attributes specified in the following table:

| Attribute              | Multiplicity | Type                                   | Description     |
|------------------------|--------------|----------------------------------------|-----------------|
| `tradeName`            | [0..1]       | *string*                               | may be present in order to specify the **trade name** (common name, service name) of the Wallet-Relying Party, if applicable.           |
| `supportURI`           | [0..1]       | *string*                               | specifies the **support URI** for the service provided by the Wallet-Relying Party, if applicable. Note that Annex I (7) of [CIR for Relying Party Registration](https://TBA) stipulates that the Wallet-Relying Party shall provide detailed contact information in form of (a) a website for helpdesk and support, (b) a phone number or (c) an e-mail address pertaining to its registration and intended use of the Wallet Units. |
| `srvDescription`       | [1..1]       | *string*                               | contains a **description of the service** provided by the Wallet-Relying Party.                              |
| `intendedUse`          | [0..*]       | [*IntendedUse*](#243-intendeduse)      | may appear one or more times in order to specify intended use cases in which the Wallet-Relying Party intends to rely on attestations of attributes of a Wallet User presented by a Wallet Unit. `IntendedUse` is **not required** from Wallet-Relying Parties that register only to act as an Intermediary.                                    |
| `isPSB`                | [1..1]       | *boolean*                              | indicates whether the Wallet-Relying Party **is a public sector body** or not.                         |
| `entitlement`          | [1..*]       | *string*                               | specifies the **set of entitlements** of the Wallet-Relying Party in form of a URI according to [RFC3986](https://www.rfc-editor.org/rfc/rfc3986.html), whereas the following URIs are defined in the present document: <ul><li> http://data.europa.eu/eudi/entitlement/Service_Provider</li> <li> http://data.europa.eu/eudi/entitlement/QEAA_Provider</li><li> http://data.europa.eu/eudi/entitlement/Non_Q_EAA_Provider</li><li> http://data.europa.eu/eudi/entitlement/PUB_EAA_Provider</li><li> http://data.europa.eu/eudi/entitlement/PID_Provider</li><li> http://data.europa.eu/eudi/entitlement/QCert_for_ESeal_Provider</li><li> http://data.europa.eu/eudi/entitlement/QCert_for_ESig_Provider</li><li> http://data.europa.eu/eudi/entitlement/rQSealCDs_Provider</li><li> http://data.europa.eu/eudi/entitlement/rQSigCDs_Provider</li><li> http://data.europa.eu/eudi/entitlement/ESig_ESeal_Creation_Provider</li></ul>     |
| `providesAttestations` | [0..*]       | [*Credential*](#244-credential)        | specifies the **set of sub-entitlements** of the Wallet-Relying Party (See [CIR for Relying Party Registration] Annex I (13)). Shall be present only if any `entitlement` of the Wallet-Relying Party is of type QEAA_Provider, Non_Q_EAA_Provider, PUB_EAA_Provider or PID_Provider, listing the attestation type(s) (`Credential`) the Wallet-Relying Party intends to issue to Wallet Units.     |
| `supervisoryAuthority` | [1..1]       | [*LegalEntity*](**link in provider specification TBA**) | specifies the competent **data protection supervisory authority** according to Article 51 of [(EU) 2016/679](http://data.europa.eu/eli/reg/2016/679/oj) in charge of supervising the Wallet-Relying Party. It provides the necessary contact information towards the Data Protection Authority through its `email`, `phone` and/or `infoURI` attributes, of which at least one SHALL be provided for an DPA registered into a Member State Registry.         |
| `usesIntermediary`     | [0..*]       | [*Intermediary*](#245)                 | if present, indicates **whether the Wallet-Relying Party depends on use of at least one Intermediary** and lists the needed information (unique Identifier and trade name) of the **Intermediaries** the registered Wallet-Relying Party depends on.               |
| `isIntermediary`       | [1..1]       | *boolean*                              | indicates whether the Wallet-Relying Party is registered to act on-behalf of End-Relying Parties (is an **Intermediary**) or not. Attribute SHALL be set to FALSE if `usesIntermediary` attribute is present. |

> Note: Topic M (User reporting unlawful or suspicious request of data to Data Protection Authorities) of the ARF adds the contact information of intended-use responsible DPA to the set of data to be registered by the Wallet-Relying Party. This information is available as OneOf[supervisoryAuthority.email, supervisoryAuthority.phone, supervisoryAuthority.infoURI] if the same DPA is valid for all intended uses of the Wallet-Relying Party.

### 2.2 Provider

The ``Provider`` superclass inherits all attributes from the more general ``LegalEntity`` superclass specified in the [Provider information specification]. WalletRelyingParty class inherits all attributes of this class.

### 2.3 LegalEntity

The ``LegalEntity`` superclass contains the attributes specified in [Provider information specification]. WalletRelyingParty class inherits all attributes of this class.

### 2.4 Auxiliary Classes

The WalletRelyingParty class has attributes that depend on a set of its own auxiliary classes (in addition to ones defined in [Provider information specification] as listed in Section 2 above) as follows: 

### 2.4.1 Claim
The ``Claim`` class is used within the definition of the [``Credential``](#244-credential) class to list required attributes within a requested attestation, and contains the following attributes:

| Attribute                 | Multiplicity | Type                   | Description                           |
|---------------------------|--------------|------------------------|---------------------------------------|
| `id`                      | [0..1]       | *string*               | Identifier of a particular claim. See [OpenID4VP] Section 6.3.            |
| `path`                    | [1..1]       | *string*               | a path pointer that specifies the path to a claim within the `Credential`. See [OpenID4VP] Sections 6.3. and 7. |
| `values`                  | [0..1]       | *string*               | An array of strings, integers or boolean values that specifies the expected values of the claim. See [OpenID4VP] Sections 6.3. and 6.4.1.                    |

### 2.4.2 Identifier
The ``Identifier`` class is used within the definition of the ``LegalEntity`` superclass and contains the attributes specified in the [Provider information specification].

#### 2.4.2.1 Unique Identifier for identification of Wallet-Relying Parties

The ARF Annex 2 high-level requirements on Relying Party Registration (**ToDo add final links**) require a Wallet Unit to be able to both identify the Wallet-Relying Party with an unique identifier and a trade name or service name of the said Relying Party, and in the optional case that Registrar issues Registration Certificates, allow association between the Access Certificate and the Registration Certificate of a Relying Party (or between an Intermediary and its End-Relying Party) based on this unique identifier. 

The [CIR for Relying Party Registration] Annex I (2) lists seven alternative identifiers and a wild card option (3)(h) to be registrable for a Wallet-Relying Party, of which **at least one** should be provided when registering a Wallet-Relying Party to a Registrar.

For the purpose of standardising a single technical unique identifier available for use in the EUDI ecosystem, identifier of type **http://data.europa.eu/eudi/id/EUID - European unique identifier (EUID)** as defined in \[[CIR for BRIS\]](https://eur-lex.europa.eu/eli/reg_impl/2021/1042/oj) SHALL be used by default in registration of a Wallet-Relying Party to a Registrar.

The other alternate identifiers listed in [CIR for Relying Party Registration] Annex I (2) MAY be registered in addition to the default unique identifier.

> Note 1: The Annex I (3)(b) 'registration number as registered in national business register' is considered redundant with EUID (3)(g) - all Member State national business registries shall provide EUIDs for all registered legal entities in their registry for cross-border purposes.

> Note 2: The set of identifiers in [CIR for Relying Party Registration] Annex I indicates all Wallet-Relying Parties to be legal persons. Registration of natural persons as Wallet-Relying Parties (an option enabled in the [Regulation]) can be followed utilising the `LegalEntity` class of [Provider information specification].

#### 2.4.3 IntendedUse

The ``IntendedUse`` class is used within the definition of the [``WalletRelyingParty``](#21-walletrelyingparty) class to specify the required information when a Wallet-Relying Party is requesting data from a Wallet Unit - for legal reference see [CIR for Relying Party Registration] Annex I paragraphs (8), (9) and (10)). 

The class contains the attributes specified in the following table:

| Attribute                 | Multiplicity | Type                   | Description                           |
|---------------------------|--------------|------------------------|---------------------------------------|
| `purpose`                 | [1..*]       | *string*               | specifies one or more **purposes** of the intended data processing according to Article 5 1. (b) of [(EU) 2016/679](https://eur-lex.europa.eu/eli/reg/2016/679/oj).            |
| `privacyPolicy`           | [1..1]       | [*Policy*](#247-policy) | specifies the privacy policy of the intended use. `Policy.type` SHALL be of type Privacy Statement.              |
| `scope`                   | [0..1]       | *string* | Wallet Units MAY support presentation requests from Wallet-Relying Parties using OAuth 2.0 scope values.        |
| `credential`              | [1..*]       | [*Credential*](#244-credential) | specifies the set of potentially **requestable attestations** which may be requested by the Wallet-Relying Party within the scope of the present intended use of data.      |
| `transactionData`         | [0..1]       | [*TransactionData*](#246-transactiondata) | if present, enables a binding between the Wallet User's authentication and the authorization to complete e.g. a payment transaction, or to sign document using QES (Qualified Electronic Signatures).       |


#### 2.4.4 Credential

The ``Credential`` class is used within the definition of the [``IntendedUse``](#243-intendeduse) class to specify the individual attestation specific information within a presentation request or upon issuance of the attestation.

| Attribute                 | Multiplicity | Type                   | Description                           |
|---------------------------|--------------|------------------------|---------------------------------------|
| `id`                      | [1..1]       | *string*               | Identifier of the attestation. See [OpenID4VP] Section 6.3.                |
| `format`                  | [1..1]       | *string*               | Specifies the format of the attestation. For valid values, see [OpenID4VP] Appendix B.  |
| `multiple`                | [0..1]       | *boolean*              | A boolean which indicates whether multiple Credentials can be returned for presentation request including this Credential. If omitted, the default value is false.  |
| `meta`                    | [0..1]       | *string*               | An object defining additional properties requested by the Verifier that apply to the metadata and validity data of the Credential. The properties of this object are defined per Credential Format.                    |
| `trusted_authorities`     | [0..1]       | *string*               | A non-empty array of objects that specifies expected authorities or trust frameworks that certify Attestation Providers, that the Wallet-Relying Party will accept. For structure and use of the object, see [OID4VP] Section 6.1.1. EUDI trust framework uses extensively Trusted Lists (TLs) as defined in ETSI 119 612, and objects of type 'etsi_tl' SHALL be used in objects when applicable Attestation Providers are managed via TLs.  |
| `require_cryptographic_holder_binding`   | [0..1] | *boolean*     | Indicates whether the Wallet-Relying Party requires a Cryptographic Holder Binding proof. See [OID4VP] Section 6.1.  |
| `claim`                   | [0..*]       | [*Claim*](#241-claim)  | A non-empty array of objects that specifies attributes in the requested attestation. See [OID4VP] Section 6.3   | 
| `claim_sets`              | [0..*]       | *string* | A non-empty array containing arrays of identifiers for elements in claims that specifies which combinations of claims for the Credential are requested. The rules for selecting claims are defined in [OID4VP] Section 6.4.1.   | 

> Note: In line with the data minimisation principle according to Article 5 1. (c) of [(EU) 2016/679](https://eur-lex.europa.eu/eli/reg/2016/679/oj) the Wallet-Relying Party may only request the minimum set of attestations (`Credentials`) and attributes (`Claims`) within those necessary for a specific intended use (described in `IntendedUse.purpose`).

#### 2.4.5 Intermediary

The ``Intermediary`` class is used within the definition of the [``WalletRelyingParty``](#21-walletrelyingparty) class. It allows to specify the attributes necessary to be able to associate an End-Relying Party with its Intermediaries it depends on, and show the trade name or service name of the Intermediary for a Wallet User upon a presentation request. See [ARF Topic 52](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/annexes/annex-2/annex-2-high-level-requirements.md#a2352-topic-52-relying-party-intermediaries), high-level requirements RPI_01 and RPI_07. 

Intermediaries are Wallet-Relying Parties with the ``WalletRelyingParty.isIntermediary`` attribute set TRUE. Validation of the registering Wallet-Relying Parties willing to act as an Intermediary is not specified in this specification.

| Attribute          | Multiplicity | Type                | Description           |
|--------------------|--------------|---------------------|-----------------------|
| `intermediaryIdentifier`     | [1..1]       | [*Identifier*](#242-identifier)       | SHALL be present in order to specify the **unique identifier** (See [Identifier](#242-identifier)) of an intermediating Wallet-Relying Party. The value SHALL be of type EUID and match with the ``WalletRelyingParty.identifier.identifier`` value of the Intermediary in question.              |
| `IntermediaryTradeName`      | [1..1]       | *string*                          | SHALL be present in order to be able to show the **trade name** of the Intermediating Wallet-Relying Party to a Wallet User. This value SHALL match with the ``WalletRelyingParty.tradeName`` value of the Intermediary in question.           |

#### 2.4.6 TransactionData

The ``TransactionData`` class is used within the definition of the [``IntendedUse``](#243-intendeduse) class. The object defined by the class is an array of strings, where `type` is the string that identifies the type of transaction data. each `credential_id` string references an attestation requested by the Wallet-Relying Party that can be used to authorise this transaction. The `transaction_details` contains any other type-specific parameters of the transaction. See [OID4VP] Section 8.4 for details.

| Attribute          | Multiplicity | Type                | Description           |
|--------------------|--------------|---------------------|-----------------------|
| `type` | [1..1] | *string* | String that identifies the type of transaction data, determining the parameters that can be included in the class object. The specific values of the type are not defined in this specification.  |
| `credential_ids` | [1..*] | *String* |  Array of strings, each referencing an attestation ([`Credential`](#244-credential)) requested by the Wallet-Relying Party that can be used to authorise the transaction. |
| `transactionDetails` | [0..1] | *string* | parameters specific to the `type` of the transaction |

#### 2.4.7 Policy (external)

The ``Policy`` class is used within the definition of the  [``IntendedUse``](#243-intendeduse)class. It contains the attributes specified in the [Provider information specification].

#### 2.4.8 LegalPerson (external)

The ``LegalPerson`` class is used within the definition of the ``LegalEntity`` 
class and allows to specify the attributes of a legal person. It contains the attributes specified in the [Provider information specification].
 
#### 2.4.9 Law (external)

The ``Law`` class is used within the definition of the ``LegalPerson`` class and contains the attributes specified in the [Provider information specification].


## 3 Common Application Programming Interface

### 3.1 API methods on registration and updating of Wallet-Relying Party data

The common Registry API write methods are defined for purposes of managing the register information of Member State Registrars, and SHALL be accessible by authorised users only. 

**To be added.**

> Note 1: This section will list the OpenAPI methods necessary to write or update information on a Registry. The methods will be only accessible for authenticated and authorised API clients. The national mechanisms to implement authentication and authorisation are left for the discretion of the Member States. Requirement to provide secure-by-design and high-availability API is especially valid for the methods listed here.

> Note 2: Write methods TBA after Section 3.2 is final.

### 3.2 API methods for registrar queries (open API)

The Registry API's read methods SHALL be open for public access. The public API SHALL provide methods for searching and querying complete data sets of registered Wallet-Relying Parties matching with provided query parameters, that can be any of:
  * **official or business registration number** (see `WalletRelyingParty.Identifier`) - will return data of list of matching Wallet-Relying Parties
  * **official company name or trade name** (see `WalletRelyingParty.legalName` and `WalletRelyingParty.tradeName`) - will return data of list of matching Wallet-Relying Parties.
  * **URL of the Wallet-Relying Party's privacy policy** (see `WalletRelyingParty.policy`) - will return data of list of matching Wallet-Relying Parties.
  * **type of entitlement** (see `WalletRelyingParty.entitlement`) - will return data of list of Wallet-Relying Parties matching with queried entitlement type.
  * **type of attestations provided** (see `WalletRelyingParty.providesAttestations`) - - will return data of list of matching Wallet-Relying Parties that provide the queried attestation type.
  * **reliance upon an Intermediary** (see `WalletRelyingParty.usesIntermediary`) - will return data of list of matching Wallet-Relying Parties that have the attribute `WalletRelyingParty.usesIntermediary` present.
  * **official company name or trade name of an Intermediary** (see `WalletRelyingParty.isIntermediary`) - will return all Wallet-Relying Parties associated with queried Intermediary information through their `WalletRelyingParty.usesIntermediary` information present in the Registy.

where query results match at least one wallet-relying party, the method response SHALL provide 
* set of information registered to matching Wallet-Relying Party (the full contents of `WalletRelyingParty` class for given instance without `WalletRelyingParty.physicalAddress` attribute), and 
* full wallet-relying party access certificate (and registration certificate, if applicable) histories, as set available by respective Certificate Authorities for Access (or Registration, where applicable) Certificates, if the Registrar is not itself acting as the respective Certificate Authority.

> Note 1: It needs to be discussed if certificate histories have an existing data model used by Certificate Authorities. Anything that closely resembles the requirement in [CIR for Relying Party Registration] is provided by Certificate Transparency registers, use of which remains open in the ARF and Cooperation Group. Specifying a **Certificate log FORMAT for X.509 or other certificate types (RPACs and RPRCs) is not in scope of the Common Format and API specification**.

> Note 2: It needs to be discussed if more narrow responses are necessary outside the minimum required by Regulation, for e.g. online verification of registered intended use contents of a known Wallet-Relying Party by Wallet Units, if a Member State does not provide Relying Party Registration Certificates for the Wallet-Relying Parties.

The OpenAPI 3.0 compatible REST API methods for above are provided in Annex A.2

### 4 References

| Reference      | Description |
|----------------------------------------|----------------------------------------------|
| [Regulation] | [European Digital Identity Regulation (EU 910/2014)](https://eur-lex.europa.eu/eli/reg/2014/910/oj/eng)  |
| [CIR for Relying Party Registration]      | **Link TBA after May 9 2025**[]()  |
| [CIR for BRIS]  | [CIR (EU) 2021/1042 on technical specifications and procedures for the system of interconnection of registers](https://eur-lex.europa.eu/eli/reg_impl/2021/1042/oj)      |
| [Common Set of Relying Party Information to be Registered]    |       [The European Commission Technical Specification on Common Set of Relying Party Information to be Registered](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts6-common-set-of-rp-information-to-be-registered.md)         |
| [Provider information specification]                            | [The European Commission Technical Specification of systems enabling the notification and subsequent publication of Provider information](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts2-notification-publication-provider-information.md) |
| [Ref TBA]           | []()                                     |

## Annex A

### A.1 JSON-Schema (normative)

The file [``json-of-common-rp-model.json``](api/ts5-json-of-common-rp-data-model.json) contains the JSON-Schema definitions corresponding to the Data Model specified in [Section 2](#2-data-model) above. It is a Wallet-Relying Party Registration -specific subset of the full data model defined in [Provider information specification].

### A.2 OpenAPI Specifications (normative)

> Note: The [OpenAPI](https://spec.openapis.org/oas/latest.html) specification of the JSON and REST based application programming interfaces described in [Section 3](#3-common-application-programming-interface) will follow in a future revision of the present document.
