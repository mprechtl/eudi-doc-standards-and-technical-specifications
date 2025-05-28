<img align="right" height="50" src="https://raw.githubusercontent.com/eu-digital-identity-wallet/eudi-srv-web-issuing-eudiw-py/34015dc3c6f52529a99e673df1d4fa69d50f7ff5/app/static/ic-logo.svg"/><br/>

# Specification of Common Interface for reporting of Relying Parties to Data Protection Authorities

## Abstract

The present document specifies the common protocols and interfaces according to Article 5a (5) (a) (x) of
[(EU) No 910/2014](http://data.europa.eu/eli/reg/2014/910/2024-10-18) used by a Wallet Unit for reporting a Wallet-Relying Party 
to the competent national data protection authority where an allegedly unlawful or 
suspicious request for data is received.

### [GitHub Discussion](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/discussions/categories/technical-specification-discussion)

## Versioning

| Version | Date        | Description                                                                                                                                                                                                                                                           |
|---------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `0.1`   | 27.05.2025 | Initial version for first discussions.                                                                                                                                                                                                                                |

## 1. Introduction

The present document specifies the **common protocols and interfaces** according to Article 5a (5) (a) (x) of
[(EU) No 910/2014](http://data.europa.eu/eli/reg/2014/910/2024-10-18) used by a Wallet Unit for invoking a suitable external application,
such as a browser or an email client, to **report a Wallet-Relying Party** to the competent 
national data protection authority where an allegedly unlawful or suspicious request for data is received.

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
> (x) for reporting a relying party to the competent national data protection authority where an allegedly unlawful or suspicious request for data is received.

Article 7 of [(EU) 2024/2982](http://data.europa.eu/eli/reg_impl/2024/2982/oj) stipulates the following:

>1.   Wallet providers shall ensure that wallet units allow wallet users to easily report wallet-relying parties to supervisory authorities established under Article 51 of Regulation (EU) 2016/679.
>
>2.   Wallet providers shall implement the protocols and interfaces for reporting wallet-relying parties in compliance with national procedural laws of the Member States.
>
>3.   Wallet providers shall ensure that wallet units allow wallet users to substantiate the reports, including by attaching relevant information to identify the wallet-relying parties, and the wallet users’ claims in machine-readable format.


### 2.2 High-Level Requirements

The set of high-level requirements after the discussions documented in
the [L+M-Discussion-Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/l%2Bm-data-deletion-and-reporting-of-wrp-to-dpa.md) 
are as follows:

| **Index**             | **Requirement specification**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RPT_DPA_01            | When prompted by the User, a Wallet Unit SHALL provide the contact details of the DPA, which supervises the Relying Party, if available, and SHALL make it easy for the User to send a report of allegedly unlawful or suspicious Relying Party presentation requests to this DPA. If these are not available, the Wallet Unit SHALL provide the contact details of the DPA of the region in which the Wallet Provider is residing. In addition, the Wallet Unit MAY also provide contact details of other DPAs taken from the "European Data Protection Board" website (https://www.edpb.europa.eu/about-edpb/about-edpb/members_en), and allow the User to choose a DPA to continue the reporting process. |
| RPT_DPA_02            | The User interface enabling a User to start the process of reporting a Wallet-relying Party to a DPA SHALL be accessible via the log provided by the Wallet Unit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| RPT_DPA_04            | Wallet providers SHALL ensure that wallet units allow wallet users to substantiate the reports, including by attaching relevant information to identify the wallet-relying parties, and the wallet users’ claims in machine-readable format.</br> Note: The log kept by the Wallet Unit will be standardized and is machine-readable in order to enable data portability. An excerpt from this log therefore can be used to substantiate the report.                                                                                                                                                                                                                                                         |
| RPT_DPA_05            | A Wallet Unit SHALL log the initiation of a report sent to the DPA in a log file so that it can be presented to the User in the dashboard (as specified in Topic 19).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| RPT_DPA_06        | The Wallet Unit SHALL take the contact details of the DPA, which supervises the Relying Party, either (in this order) from a) included in  the RPRC in the log entry, b) included in the RPAC in the log entry, c) looked up by the Wallet Unit from the RP Registry, based on the Subject of the RPAC in the log entry. </br> The contact information includes  at least one of email address, phone number, or a URL of a webform.                                                                                                                                                                                                                                                                      


## 3. Specification of Protocols and Interfaces

As agreed upon within the discussions documented in the [L+M-Discussion-Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/l%2Bm-data-deletion-and-reporting-of-wrp-to-dpa.md) 
the general approach is to utilise the already existing interfaces and processes of the 
competent national data protection authorities as much as possible and abstain from the creation of 
additional technical interfaces.

The resulting system architecture is depicted in the following figure:

![Overview](img/ts8-architecture.svg) 

As shown in the figure above, there are the following interfaces:

* **(I1)** - is a user-friendly user interface, which allows to access the functionality of the Wallet Unit and which in particular
  allows to report a Wallet-Relying Party to the competent national data protection authority where an allegedly unlawful or suspicious request 
  for data is received. This functionality shall be immediately accessible when such a request has been received or later based on the 
  information stored in the Transaction Log. Wallet units shall allow wallet users to substantiate the reports, 
  including by attaching relevant information to identify the wallet-relying parties, and the wallet users’ claims 
  in machine-readable format. The technical details of this format will be derived from the normalised format of the 
  transaction log, which will be specified based on the related discussions within the [H-Discussion-Paper](https://github.com/eu-digital-identity-wallet/eudi-doc-architecture-and-reference-framework/blob/main/docs/discussion-topics/h-transaction-logs-kept-by-the-wallet.md).

> **Note 1**: Should there be more specific requirements for the UI of the Wallet Unit?

* **(I2)** - is a platform-specific interface, which allows to invoke a mail client on the platform with a 
  ``mailto:`` link with the email address of the competent national data protection authority, which supervises the 
  Wallet-Relying Party under consideration. The email address may be provided as part of the provided Wallet-Relying Party 
  Registration Certificate, if available, or the provided Wallet-Relying Party Access Certificate, or within the ``supervisoryAuthority`` 
  within the registration information of the Wallet-Relying party according to [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md). The wallet unit shall also support 
  the option to send a report to the data protection authority responsible for supervising the Wallet Provider or to  
  look up other data protection authorities from the website of the "European Data Protection Board" 
  (https://www.edpb.europa.eu/about-edpb/about-edpb/members_en).

> **Note 2**: Should we specify more details or an example here, or can we simply assume that the Wallet Providers will take care
> of the invocation and suitable texts?

* **(I5)** - represents a logical interface given by the mail system of the data protection authority, which is 
  assumed to exist, if the registration information with respect to the ``supervisoryAuthority`` of the Wallet-Relying Party 
  according to [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md) contains an email address for support. 

> **Note 3**: Should we require, or at least recommend, that an email address of the competent data protection authority shall be registered 
> in [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md)?  
>
> **Note 4**: We should also ensure that the mail address of the DPA is part of the Wallet-Relying Party Registration Certificate, if 
> it exists, and the Wallet-Relying Party Access Certificate, as this is known to be available in any case.

* **(I4)** - is a platform-specific interface, which allows to invoke a browser on the platform to open a specific 
  URL of the data protection authority in charge of supervising the Wallet-Relying Party, which has submitted an
  allegedly unlawful or suspicious request.

* **(I3)** - represents a logical interface given by the web application provided by the competent national authority 
 responsible for the supervision of the Wallet-Relying party at the given URL contained in the ``supervisoryAuthority`` 
 of the Wallet-Relying party according to [STS-TS5](https://github.com/eu-digital-identity-wallet/eudi-doc-standards-and-technical-specifications/blob/main/docs/technical-specifications/ts5-common-formats-and-api-for-rp-registration-information.md).

> **Note 5**: Should there be more specific requirements, or recommendations, for the web application of the data protection authority?
