<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Specification of Common Interface for Data Deletion Requests to Relying Parties

## Abstract

The present document specifies the common protocols and interfaces according to Article 5a (5) (a) (ix) of
[(EU) No 910/2014](http://data.europa.eu/eli/reg/2014/910/2024-10-18) used by a Wallet Unit for invoking a suitable external application, 
such as a browser or an email client, to send data deletion requests according to Article 17
[(EU) 2016/679](http://data.europa.eu/eli/reg/2016/679/oj) for personal data, which was previously
provided by a Wallet Unit, to Wallet-Relying Parties.   

### [GitHub Discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/categories/technical-specification-discussion)

## Versioning

| Version | Date        | Description                                                                                                                                                                                                                                                           |
|---------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `0.1`   | 27.05.2025 | Initial version for first discussions.                                                                                                                                                                                                                                |

## 1. Introduction

The present document specifies the **common protocols and interfaces** according to Article 5a (5) (a) (ix) of
[(EU) No 910/2014](http://data.europa.eu/eli/reg/2014/910/2024-10-18) used by a Wallet Unit for invoking a suitable external application,
such as a browser or an email client, to send **data deletion requests** according to Article 17
[(EU) 2016/679](http://data.europa.eu/eli/reg/2016/679/oj) for personal data, which was previously
provided using a Wallet Unit, **to Wallet-Relying Parties**.

The general approach with respect to the protocols and interfaces specified in the present document are 
based on the result of the related discussions documented in the corresponding [L+M-Discussion-Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/l%2Bm-data-deletion-and-reporting-of-wrp-to-dpa.md). 

## 2. Requirements

### 2.1 Legal Requirements

Article 5a (5) of the regulation [(EU) No 910/2014](http://data.europa.eu/eli/reg/2014/910/2024-10-18) requires that 

>European Digital Identity Wallets shall, in particular:
>
>(a) support common protocols and interfaces:
>
> [...]
>
> (ix) for requesting a relying party the erasure of personal data pursuant to Article 17 of Regulation (EU) 2016/679;

Article 6 of [(EU) 2024/2982](http://data.europa.eu/eli/reg_impl/2024/2982/oj) stipulates the following:

>1. Wallet providers shall ensure that wallet units support protocols and interfaces allowing wallet users to request from wallet-relying parties, with whom they have interacted through those wallet units, the erasure of their personal data provided through those wallet units, in accordance with Article 17 of Regulation (EU) 2016/679.
>
>2.   The protocols and interfaces referred to in paragraph 1 shall allow wallet users to select the wallet-relying parties to which data erasure requests are to be submitted.
>
>3.   Wallet units shall display to the wallet user previously submitted data erasure requests made through those wallet units.

### 2.2 High-Level Requirements

The set of high-level requirements after the discussions documented in
the [L+M-Discussion-Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/l%2Bm-data-deletion-and-reporting-of-wrp-to-dpa.md) 
are as follows:

| **Index**              | **Requirement specification**                                                                                                                                                                                                                                                                                                 |
|------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DATA_DLT_01            | A Wallet Provider SHALL ensure that its Wallet Units support the technical specifications mentioned in DATA_DLT_02, allowing a User to request from a Relying Party the erasure of their attributes that were presented by that Wallet Unit to that Relying Party, in accordance with Article 17 of Regulation (EU) 2016/679. |
| DATA_DLT_02            | The Commission SHALL, in cooperation with the Member States, develop technical specifications for a Wallet Unit interface allowing a Wallet Unit to send attribute deletion requests to Relying Parties with whom it has interacted in the past.                                                                              |
| DATA_DLT_03            | A Wallet Instance SHALL provide a function where the User may select one Relying Party for which an attribute deletion request must be submitted.                                                                                                                                                                             |
| DATA_DLT_05            | A Wallet Unit SHALL include attribute deletion requests in a log so they can be presented to the User via the dashboard (as specified in [Topic 19](#a2319-topic-19---user-navigation-requirements-dashboard-logs-for-transparency)).                                                                                         |
| DATA_DLT_06            | The log SHALL also document the initiation of a data deletion request and include as a minimum: - Date and time of attribute deletion request, - Relying Party to which the request was made, - Attributes requested to be removed.                                                                                   |
| DATA_DLT_07        | While the Wallet-Relying Party is responsible for choosing appropriate authentication mechanisms before executing a data deletion request, it is RECOMMENDED to use the authentication and signature facilities offered by the Wallet Solutions for this purpose.                                                         |


## 3. Specification of Protocols and Interfaces

As agreed upon within the discussions documented in the [L+M-Discussion-Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/l%2Bm-data-deletion-and-reporting-of-wrp-to-dpa.md) 
the general approach is to utilise the already existing interfaces and processes of the 
Wallet-Relying Parties as much as possible and abstain from the creation of 
additional technical interfaces.

The resulting system architecture is depicted in the following figure:

![Overview](img/ts7-architecture.svg) 

As shown in the figure above, there are the following interfaces:

* **(I1)** - is a user-friendly user interface, which allows to access the functionality of the Wallet Unit, 
  browse through the Transaction Log, search for transactions within a given period in time or with a given Wallet-Relying Party 
  and in particular allows to request the deletion of the previously provided personal data according to Article 17
  [(EU) 2016/679](http://data.europa.eu/eli/reg/2016/679/oj) at a specific Wallet-Relying Party.

> **Note 1**: Should there be more specific requirements for the UI of the Wallet Unit?

* **(I2)** - is a platform-specific interface, which allows to invoke a mail client on the platform with a 
  ``mailto:`` link with the email address of the Wallet-Relying Party, a suitable text for the subject of the mail 
  and a proposal of a text for the body of the mail in which the data subject asks for the deletion of the personal data, 
  which has previously been provided by a Wallet Unit.

> **Note 2**: Should we specify more details or an example here, or can we simply assume that the Wallet Providers will take care
> of the invocation and suitable texts?

* **(I5)** - represents a logical interface given by the mail system of the Wallet-Relying party, which is 
  assumed to exist, if the registration information with respect to the ``supportURI`` of the Wallet-Relying party according to [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md) 
  contains an email address for support. 

> **Note 3**: Should there better be different elements for email, web and phone instead of a single ``supportURI`` element in 
> [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md)? 

* **(I4)** - is a platform-specific interface, which allows to invoke a browser on the platform to open a specific 
  URL of the Wallet-Relying Party, which has been provided in the corresponding registration information in 
  [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md). 
  The web application available at this URL is assumed to provide further information how to request the 
  deletion of the personal data, which has previously been provided by a Wallet Unit.

* **(I3)** - represents a logical interface given by the web application provided by the Wallet-Relying party at the given  
  URL contained in the ``supportURI`` of the Wallet-Relying party according to [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md).

> **Note 4**: Should there be more specific requirements for the web application of the Wallet-Relying Party?

* **(I6)** - is the normalized and mandatory technical interface of a Wallet Unit based on the [OID4VP](https://openid.net/specs/openid-4-verifiable-presentations-1_0.html) protocol, 
  which allows to ask for the strong identification of the data subject or the creation of a qualified electronic signature for a provided
  text, which asks for the deletion of previously provided personal data.   

> **Note 5**: Should there be further recommendations with respect to the text, which is requested to be signed to authorize the data deletion request?
