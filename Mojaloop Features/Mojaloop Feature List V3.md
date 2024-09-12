# Mojaloop Feature List

**DRAFT VERSION - SUBJECT TO REVIEW**

**9th September 2024**  
Authors: Paul Makin, James Bush  
Version: Draft 0.3  
# About Mojaloop
Mojaloop is open source instant payments software that interconnects disparate financial institutions in a manner that promotes financial inclusion and provides robust risk management. It is available for use by any body that wishes to use it to implement and operate a payments scheme.

The core functions that Mojaloop provides to connected financial institutions are:   
1. Payee address or **alias resolution**, ensuring that the correct payee account is reliably identified;
2. **Clearing** of payments end to end, with robust measures that remove any element of doubt about the success of a transaction;
3. **Settlement** of cleared transactions between financial institutions using a model agreed between those institutions, and according to a predefined schedule.   

**Note** In Mojaloop terms, a DFSP - or Digital Financial Service Provider - is a generic term for any financial institution, of any size or status, that is able to transact digitally. It applies equally to the largest international bank and the smallest Microfinance Institution or mobile wallet operator. "DFSP" is used throughout this document.   

&nbsp;
# Purpose of This Document  
This document catalogues the features of Mojaloop, independent of implementation.  It is intended to both inform potential adopters of the features they can expect and (where appropriate) how those features can be expected to function, and to inform developers of the features they must implement in order for their efforts to be accepted as an official instance of Mojaloop.  
 
The Mojaloop Foundation (MLF) defines an implementation as being an official instance of Mojaloop if it implements all of the features of Mojaloop, without exception, and they pass the standard set of Mojaloop tests.   

This feature list covers the following aspects of Mojaloop:
- **Transactions,** describing the use cases supported, the APIs, how a Mojaloop transaction proceeds, and the aspects of a Mojaloop transaction that make it uniquely suited to the implementation of an inclusive instant payments service.
- **Risk Management,** setting out the measures taken to ensure that no DFSP participating in a Mojaloop scheme is exposed to any counterparty risk, and that the integrity of the scheme as a whole is protected.
* **Product and Operational Features**, such as portals for user and service management, and the configuration and operation of a Mojaloop Hub.     
* **Engineering Principles**, such as algorithmic adherence to the Mojaloop specification, code quality, security practices, scalability and performance patterns (amongst others).  
- **Invariants,** setting out the development and operational principles to which any Mojaloop implementation must adhere.   

&nbsp;
# Scope
This feature list is subject to amendment as the Mojaloop ecosystem continues to develop.   
&nbsp;
# Transactions
## Core Use Cases
A Mojaloop Hub supports a range of different transaction types.  
**DFSP-initiated**
- Account to account, or person to person (P2P)
- Merchant Request to Pay
- Nation-scale bulk payments lists, in support of social payments and salaries.  

**PISP-initiated**
- Account association/continuing authorisation
- Request to Pay

## Value-Add Use Cases
In addition to the core use cases, Mojaloop supports the implementation of a number of more complex use cases, which add external features and are layered over the top of the core use cases.  

The most prominent of these is Merchant Payments, which adds a Merchant Registry function externally to the Mojaloop Hub, and supports the generation of QR codes for the routing of payments from customers with smartphones to merchants, linked to merchants' existing systems. This is in parallel to support for USSD-based merchant payments. 

## Mojaloop APIs

The Mojaloop Hub supports four core APIs:
1. FSP Interoperability (FSPIOP) API   
This API is the principal transactional API, supporting direct connections with Participants. It supports the P2P, Request To Pay (RTP), Bulk Payments and the DFSP-side of PISP transactions.
2. Administration API   
The purpose of the Administration API is to enable Hub Operators to manage admin processes around:   
- creating/activating/deactivating participants in the Hub
- adding and updating participant endpoint information
- managing participant accounts, limits, and positions
- creating Hub accounts
- performing Funds In and Funds Out operations
- creating/updating/viewing settlement models
- retrieving transfer details
3. Settlement API   
The settlement API is used to manage the settlement process.
4. Third-party Payment Initiaion (3PPI/PISP) API
Third party payment arrangements - payments initiated by fintechs on behalf of their customers from accounts held by those customers at DFSPs connected to the Mojaloop Hub. 
## Phases
Mojaloop is a payment clearing hub: it clears (routes and guarantees) payments between DFSP accounts, and integrates with a settlement partner to move the actual funds between the two DFSPs at a later time, according to an agreed settlement schedule.  

All Mojaloop transactions are asynchronous (to ensure the most efficient use of resources), and proceed through three phases:
1. **Discovery,** when The Payer's DFSP works with the Mojaloop Hub to determine where the payment should be sent. This phase resolves an alias to a specific Payee DFSP and account.
2. **Agreement,** when the two DFSP parties to the transaction agree that the transaction can go ahead, and on what terms.
3. **Transfer,** when the actual transfer is committed between the two DFSPs.   

This three phase approach has been designed to minimise the risk of transactions failing, so that we remove the need for reconciliation by DFSPs, eliminate most causes of disputes, and so minimise costs for all parties. This, together with the Mojaloop approach to Risk Management, ensures that even the smallest MFI and the biggest international bank can participate on equal terms, with neither imposing risk on the other.

## Unique Transaction Characteristics

Most, if not all, of the functions Mojaloop supports are also offered by other payment clearing hubs. What differentiates Mojaloop is:  
1. **The Agreement of Terms, or Quotation,** phase of a transaction, which allows two DFSPs to agree that a transaction can take place *before* it is committed. This supports some of the most complex aspects of trasnactions between differing types of Participant; a Payee DFSP can verify that the customer's account can receive the payment, that it hasn't been suspended, or the payment won't breach transaction or balance limits. If that's all OK, then the Payee DFSP can accept the transaction, subject to any fees that it will charge, and the Hub can add its own charges. Only if the Payer DFSP, and the Payer him/herself, agree to those charges will the transaction then take place. This removes the uncertainty, and all but guarantees that the transaction will succeed, even before it happens.
2. **End to End Non-Repudiation** in the Transfer phase of the transaction guarantees that the transaction will only be committed if *both* the Payer and the Payee DFSPs accept that it is; and, once committed there is cryptographic evidence of this, and neither party can repudiate the transaction. This obviates the need for reconciliation, drives down on the level of disputed transactions, and so substantially reduces costs for all participants.
3. **The PISP API is made available through the Mojaloop Hub,** not by individual Participants. Consequently a fintech can integrate with the Hub and immediately be connected to all connected DFSPs, rather than needing to complete an API integration with the all individually. This substantially reduces costs and increases reliability for fintechs and their customers.

# Risk Management
A key aspect of the operation of a payment scheme that is built around a Mojaloop hub is the management of risk between transacting parties, who will themselves have different appetities for risk. The principles applied are:
1. All Participants (FSPs) are required to deposit liquidity with the Scheme operator. This liquidity can only be withdrawn, in whole or in part, from the Scheme with the agreement of the Scheme Operator.
2. A transaction will only be cleared (during the Quotation phase) if there is sufficient liquidity available to cover it, as measured against the liquidity balance and the DFSP's current Position (the net total of already cleared transactions, as either payer or payee).
3. A cleared transaction will be added to the DFSP's Position.
4. During settlement, a negative Position will be debited from the Liquidity balance and transferred to the settlement partner for distribution to the creditors; a positive Position will be credited to the Liquidity balance by the settlement partner.
5. A DFSP is expected to manage their liquidity, adding to it if it drops to a level where anticipated transaction values will result in failed transactions, or withdrawing some (on application to the scheme operator) if the value is too high.
6. Where the settlement partner is not available 24/7, a DFSP can deposit extra balance to their liquidity account, for example to cover anticipated transactions over a holiday period. A DFSP can manage this extra balance using a Net Debit Cap (NDC), which might be used for example to limit the use of liquidity to the levels that are expected on a particular day, in order to ensure that the DFSP can continue to trade throughout the whole of the holiday period. The NDC is used in tandem with the liquidity balance in the authorisation of transactions during the Quotation phase.   

&nbsp;
# Product And Operational Features
The aspects of product and operational features that are addressed are:
*	User Management
*	Participant Management
*	Transaction Review
*	Settlement
*	Logging & Audit
*	Hub Management
*	Oracle Management
*	Participant Portal
*	Common Requirements    

These functions are provided through Mojaloop's Business Operations Framework (BOF), which not only provides the core functions described here but also provides a set of APIs to allow a Hub operator to extend these portals, and create news ones, to meet their specific requirements.

The BOF channels all activity through a single Identity and Access Management (IAM) framework, which incorporates Role Based Access Controls (RBACs) to manage an individual's access to the management capabilities of the Mojaloop Hub. 

Access to each of the above functions is implemented using the BOF, and is managed using the IAM and RBAC.  
## User Management
These features relate to the management of the hub operator's staff using the integrated IAM module, rather than management of the service itself.

1. Create and manage user accounts for staff of the hub operator and participants through the IAM portal.   
2. Define roles associated with access to various sub-elements of the portals.   
3. Assign roles to user accounts, defining which users have access to which features of the portals.  
4. For sensitive functions, define a maker/checker requirement, including the roles that must be held by the maker and the checker, and any restrictions.  
5. Enable/disable user accounts.  
6. Create user accounts for participants, to facilitate self service through the Participant Portal (when it is implemented).  
7. Allow a user to be a maker and a checker (but not of their own work).   

Note that the Participant Portal is not yet implemented by any Mojaloop hub, and so (6) is not currently a requirement.

## Participant Management 
Features that allow a hub operator to manage a participating DFSP (note that this is distinct from the Participant Portal).  

1. Onboarding a participating DFSP.  
2. Define and manage end-points (including specification of certificates and source IPs)
3. Manage Participant contacts (name, email, MSISDN, role etc). 
4. Define thresholds (for notifications). 
5. Define and manage accounts for Participant by type, currency.  
6. Disable a participating DFSP (though it should not be possible to disable a DFSP with outstanding/unsettled transactions).  
7. Pause/Resume a Participant's connection.  
8. Assign/adjust liquidity (for multiple currencies), controlled with maker/checker. 
9. Assign/adjust a Net Debit Cap (NDC) for each Participant, controlled by maker/checker, to include two options for NDC: fixed value (manual adjustment after each liquidity change), or variable (as a fixed percentage of available liquidity). 
10. Restrict Participant connection to send or receive only. 

## Transaction Review
The hub operator's staff need to be able to find details about a transaction, whatever its status. They should be able to search by: 
- Date/time range 
- Payer or Payee DFSP (Participant) 
- Value 
- Mojaloop transaction ID   
- Transfer state   
- Settlement Batch ID   
- Transaction type   
- Error code   

The search should return a list of all the transactions matching the search criteria, each clickable for a detailed view, which should give access to: 
- All details held by the Mojaloop Hub, grouped in a set of sub-windows to improve usability  

This should include the settlement batch ID which should itself be clickable to allow the operator to review the settlement status.

## Settlement
Management of the settlement function of the Mojaloop Hub needs to be robust and reliable. The related features are:   

1. To define the settlement model to be used for the service
2. To close a settlement window either manually or automatically, according to a predefined schedule
3. To automatically create all of the necessary settlement files for integration with settlement partner(s) when a settlement window is closed.
4. To review the positions of all Participants in the settlement window
5. Once settlement has been completed/finalised, to automatically update the positions and current available liquidity based on reports from the settlement partner(s)
6. To provide tools to support the integration between the Mojaloop Hub and settlement partner(s).

Note that a settlement window will always be open, and transactions will be added to the open settlement window as they are processed. This means that the creation of a new settlement window is automatic when the existing settlement window is closed.   
## Logging & Audit
The Mojaloop Hub provides a range of tools that support the logging and audit of operator activity, in addition to low level audit functions for the detailed analysis of transaction processing (which are defined elsewhere in this document). These tools have been developed with the requirements of both Hub operator management and externakl auditors in mind.  
1. All modifications arising from hub operator activity (including user management) are logged in an uneditable data store, with the operator's credentials attached
2. "Auditor" is a default hub user role; auditors have unrestricted read access to the logs. 
3. An audit portal is available, which has Search/refine functionality available
4. Log/audit entries include changes to Hub configuration.
## Hub Management
There are some basic requirements for the configuration of a Mojaloop Hub, defining the service that it supports.  
1. Define the currencies a Hub supports, using international standard representations.
2. When a currency is added, settlement and reconciliation accounts for that currency are automatically created
3. View the balances in Hub operating accounts (settlement and reconciliation, replicated per currency supported)
4. Delete a currency from a Hub's configuration. Deletion is rejected if either the settlement or reconciliation account has a non-zero balance.
5. When a currency is deleted, the associated settlement and reconciliation accounts shall also be deleted.
6. Add/view/delete the CA certificates needed for normal operation.  

## Oracle Management
Management of the oracles used for the resolution of aliases to DFSPs/Participants.
1. View the registered oracles
2. Register an Oracle
3. Define an endpoint
4. Test the health of an oracle   

## Participant Portal
Currently, the Mojaloop Hub does not offer a Participant Portal. Instead, this functionality is provided by another open source project, Payment Manager (https://github.com/pm4ml).  

## Reports
Mojaloop provides a flexible reporting engine as part of the Hub Operations Framework, which allows a Hub operator's staff to design and generate a wide range of reports based on data held in Mojaloop's databases and ledgers. The Framework also supports the integration of those reports into any of the operator portals, allowing the reports to be generated as required by operations staff.  
 
This includes the reports relating to settlement.  

&nbsp; 
# Engineering Principles
This section details the principles behind the engineering aspects of the Mojaloop Hub.
## Logging
Industry-standard logging mechanisms for containers (stdout, stderr) are the default.
## Transfers
1. Resource identifiers are unique within a scheme and enforced by the hub.
2. API methods which can potentially return large result sets are paged by default.
3. Settlement model lookups use the currencies of payer and payee DFSP.
4. Resources/entities are typed so they can be differentiated
5. Names (of objects, methods, types, functions etc…) are clear and not open to misinterpretation.
## Accounts & Balances
1. Ledger implementation uses a strongly consistent underlying data store.
2. Critical financial data is replicated to multiple geographically distributed nodes in a manner that is strongly consistent and highly performant, such that the failure of multiple physical nodes will result in zero data loss.
## Participants
1. Participant connectivity concerns are handled at a gateway layer to facilitate the use of industry standard tooling.
## Scalability and Resilience
1. Overall system transfer throughput (all three transfer phases) is scalable in as near linear manner as possible by the addition of low specification, commodity hardware nodes.
2. Business critical data can be replicated to multiple geographically distributed nodes in a manner that is strongly consistent and highly performant; Failure of multiple physical nodes will result in zero data loss.
## Mojaloop Specification
1. JWS is supported
2. Mutually authenticated (x.509) TLS v1.2 should be supported between participants and the hub
## Deployment
1. Helm charts are available with default values suitable for production deployment scenarios.
## General
1. Context specific processing is done once and results cached in memory where required later in the same call stack.
2. All log messages contain contextual information. 
3. Failures are anticipated and handled as gracefully as possible.
4. Cross process / network queries ask for only the data required.
5. Layers of abstraction are kept to an absolute minimum.
6. Interprocess communication uses the same transport mechanism wherever possible.
7. Aggregates are stateless.   

&nbsp;
# Invariants
## General Principles
**The primary function of the platform is to clear payments in real-time and facilitate regular settlement, by the end of the value day.**
1. The platform allows participants to clear funds immediately to their customers while keeping the risks and costs associated with this to a minimum.
2. The platform supports per-transfer checks on available liquidity where these are required in support of the first objective.
3. The hub is optimized for critical path.
4. Intra-day Automated Settlement; configured by scheme and implementation using recommended settlement models for financial market infrastructure.

**The hub supports fully automatic straight-through processing.**
1. Straight through processing helps reduce human errors in the transfer process which ultimately reduces costs.
2. The automated nature of straight through processing leads to faster value transfers between end customers.    

**The hub requires no manual reconciliation as the protocol for interacting with the hub guarantees deterministic outcomes.**

1. When a transfer is finalized, there can be no doubt about the status of that transfer (alternatively, it is not finalized and active notice provided to participants).
2. The hub guarantees deterministic outcomes for transfers and is accepted by all participants as the final authority on the status of transfers.
3. Determinism means individual transfers are traceable, auditable (based on limits, constraints), with final result provided within guaranteed time limit.
4. For the avoidance of doubt, batch transfers are processed line-by-line with potentially differing deterministic outcomes for each.

**Transaction setup logic, which is use case-specific, is separated from the policy-free transfer of money.**

1. Transaction details and business rules should be captured and applied between counterparties prior to the quoting phase and is out of scope for Mojaloop.
2. The agreement phase establishes a signed use-case specific transaction object which incorporates all transaction-specific details.
3. The transfer phase orchestrates the transfer of retail value between institutions for the benefit of the counterparties (i.e only system limit checks are applied) and without reference to transaction details.
4. No additional transaction-specific processing during the transfer phase.

**The hub doesn’t parse or act on end-to-end transaction details; transfer messages contain only the values required to complete clearing and settlement.**

1. Checks & validations during the transfer step are only for conformance to scheme rules, limit checks, signature authentication, and validation of payment condition and fulfillment.
2. Transfers that are committed for settlement are final and are guaranteed to settle under the scheme rules.

**Credit-push transfer semantics are reduced to their simplest form and standardized for all transaction types.**

1. Simplifies implementation and participant integration as many transaction types and use-cases can reuse the same underlying value transfer message flow.
2. Abstracts use-case complexity away from the critical path.

**Internet-based API service hub is not a “message switch.”**

1. The service hub provides real-time API services for participants to support retail credit-push instant transfers.
2. API services such as ID-to-participant look-up, transaction agreement between participants, submission of prepared transfers, and submission of fulfillment advice.
3. Auxiliary API services for participants are provided to support onboarding, position management, reporting for reconciliation, and other non-realtime functions not associated with transfer processing.
4. All messages are validated for conformance to the API specification; non-conforming messages are actively rejected with a standardized machine-interpretable reason code.

**The hub exposes asynchronous interfaces**

1. To maximize system throughput.
2. To isolate leaf-node connectivity issues so they don't impact other end-users.
3. To enable the hub system to process requests in its own priority order and without holding an active connection-per-transfer.
4. To handle numerous concurrent long-running processes through internal batching and load balancing.
5. To have a single mechanism for handling requests (Examples are transactions such as bulk or those needing end user input or that span multiple hops).
6. To better support real world networking as issues with connection speed and reliability for one participant should have minimal impact on other participants or system availability more generally.

**The transfer API is idempotent**

1. This ensures duplicate requests may be made safely by message originators in conditions of degraded network connectivity.
2. Duplicate requests are recognized and result in the same outcome (valid duplicates) or are rejected as duplicate (when not allowed by specification) with reference to the original.

**Finalized Transfer records are held for a scheme-configurable period to support scheme processes such as reconciliation, billing, and for forensic purposes**

1. It is not possible to query the "sub-status" of an in-process Transfer; the API provides a deterministic outcome with active notice provided within the guaranteed service time.

**Transfer records for finalized transfers are held indefinitely in long-term storage to support business analysis by the scheme operator and by participants (through appropriate interfaces)**

1. Availability of Transfer records may lag online process finality to accommodate separation of record-keeping from real-time processing of Transfer requests.

**Hub may serve as a proxy for some inter-participant messages (e.g. during the Agreement phase) to simplify interconnection but without parsing, storing (other than to support forwarding), or further processing the messages.**

1. In some messaging flows e.g. party lookup, it may be desirable for participants to have a single point of contact for routing of scheme related messages, even when the messages are not intended for the hub, nor require any inspection or other processing.   

## Security and Safety
**API messages are confidential, tamper-evident, and non-repudiable.**

1. Confidentiality is required to protect the privacy of the participants and their customers.
2. There are legal requirements in many regulatory domains where Mojaloop is expected to operate and as such, the hub must employ best practices to ensure that the privacy of the participants and their customers is protected.
3. Tamper-evident integrity mechanisms are required to ensure that messages cannot be altered in transit.
4. To ensure the integrity of the overall system, each recipient of a message should be able to independently tell, with a high degree of confidence, that the message was not altered in transit.
5. Public key cryptography (digital signing) provides the current best known mechanism for tamper-evident messaging
6. The security of the senders private key is critical.
7. Scheme rules must be established to clarify the responsibilities for key management and the potential for financial liability upon compromise of a private key.
8. Non-repudiation is required to ensure that the message was sent by the party which purported to send it and that provenance can not be repudiated by the sender.
9. This is important for determining the liable party during audit and dispute resolution processes.

**API messages are Authenticated upon receipt prior to acceptance or further processing**

1. Authentication gives a degree of confidence that the message was sent by the party which purported to send it.
2. Authentication gives a degree of confidence that the message was not sent by an unauthorized party.

**Authenticated Messages are not acknowledged as accepted until safely recorded to permanent store**

1. The Mojaloop API assigns significant scheme related business meaning to certain HTTP response codes at various points in transaction flows:
2. Certain HTTP responses, e.g. "202 Accepted", are intended to provide financial guarantees to participants and as such must only be sent once the receiving entity is confident it has made safe, permanent record(s) in support of:
3. Facilitating system wide recovery to a consistent state after failure(s) in one or more distributed components/entities.
4. Accurate settlement processes
5. Audit and dispute resolution processes
6. For example a "202 Accepted" from the hub to the payee participant upon receipt of a transfer fulfilment message indicates a guarantee of transaction settlement to the payee.
7. The Mojaloop API is designed to operate safely under imperfect network conditions and as such has built in support for retries and state synchronisation between participants.

**Three levels of communication security to ensure integrity, confidentiality, and non-repudiation of messages between an API server and API client.**

1. Secure Connections: mTLS required for all communications between the scheme and authorized participants.
2. Ensures communications are confidential, between known correspondents, and communications are protected against tampering.
3. Secure Messages: JSON message content is cryptographically signed according to the JWS specification.
4. Ensures recipients that messages were sent by the party which purported to send them and that provenance can not be repudiated by the sender.
5. Secure Terms of Transfer: Interledger Protocol (ILP) between Payer and Payee participants.
6. Protects the integrity of the payment condition and its fulfillment.
7. Limits the time within which a transfer instruction is valid.

**To ensure system is arithmetically consistent, only fixed point arithmetic is used.**

1. For the avoidance of doubt, floating point calculations may lose accuracy and must not be used in any financial calculation.
2. See Level One Decimal Type representation and forms.
3. This specification enables seamless interchange with XML-based financial systems without loss of precision or accuracy

## Operational Characteristics
**Baseline system demonstrated on minimal hardware supports clearing 1,000 transfers per second, sustained for one hour, with not more than 1% (of transfer stage) taking more than 1 second through the hub.**

1. This measurement includes all necessary hardware and software components with production grade security and data persistence in place.
2. This measurement includes all three transfer stages: discovery, agreement, and transfer.
3. This measurement does not include any participant introduced latency.
4. A one hour period is a reasonable approximation of a demand peak for a national payment system.
5. A lower unit cost to scale than to initially provisioned.
6. 1000 transfers (clearing) per second is a reasonable starting point for a national payment system.
7. 1% of transfers (clearing) taking more than 1 second is a reasonable starting point for a national payment system.
8. Mojaloop schemes should be able to start at a reasonable cost point, for national financial infrastructure, and scale economically as demand grows.

**The hub is highly available and resilient to failures.**

1. Definition of "highly available":
2. In this instance we define the term "highly available" as meaning "the ability to provide and maintain an acceptable level of service in the face of faults and challenges to normal operation."
3. Although schemes may determine their own definition of what constitutes an "acceptable level of service", Mojaloop makes certain contributing tradeoff choices:
4. When fault modes permit, service is degraded across the entire participant population rather than individual participants suffering total outages while others remain serviceable.
5. The hub has no single point of failure; meaning that it continues to operate with minimum degradation of service in the event of a failure of any single component.
6. Multiple active instances of each component are deployed in a distributed manner behind load balancers.
7. Each active component instance can handle requests from any client/participant meaning no single participant loses the ability to transact in the event of a failure of any single component.
8. Given appropriate infrastructure to operate upon, the Mojaloop software can be deployed in configurations which deliver 99.999% uptime (five nines) overall.
9. This includes active:active and active:passive multiple, geographically distributed data center configurations where both services and data are replicated across multiple physical nodes which are expected to fail independently.
10. Note that it is expected that nodes in replication groups (and/or clusters) will be located in diverse physical locations (racks and/or data centres) with independent power supplies and network interconnects.
11. Should multiple component failures occur which have not been mitigated either in the Mojaloop software, deployment configuration or infrastructure, the Mojaloop API provides mechanisms for each entity in the scheme to recover to a consistent state, with the hub being the ultimate source of truth upon full restoration of service.
12. Also see further points relating to resistance to data loss in the event of failures.
13. Given that Mojaloop schemes are intended to form parts of national financial infrastructure they must have as close to zero downtime as possible, given reasonable cost constraints.
14. Failures in hardware and software components are to be expected, even in the highest quality components available. Best practice suggests these failures should be anticipated and planned for as much as possible in the design of the hub with a view to minimising loss or degradation of service and/or data.
15. For the avoidance of doubt this means the tradeoffs chosen favour overall service availability and state consistency over performance. I.e:
- All participants can continue to transact at a reduced rate rather than some participants being unable to transact at all.
- Inconsistencies in state between scheme entities are resolvable post service restoration via the Mojaloop API, with minimal manual reconciliation necessary; the hub being the ultimate source of truth.

**The hub is resistant to loss of data in the event of failures.**

1. Given appropriate infrastructure to operate upon, the Mojaloop software can be deployed in configurations which reliably replicate data across multiple, redundant physical storage nodes prior to processing.
2. Database engine components which are provided by the Mojaloop deployment mechanisms support the following:
- Primary:secondary asynchronous replication.
- Primary:primary synchronous replication.
-  Synchronous quorum consensus algorithm based replication.
3. The replication mechanisms available are dependant on the particular storage layer and database technologies employed.
4. Should multiple component failures occur which have not been mitigated either in the Mojaloop software, deployment configuration or infrastructure, the Mojaloop API provides mechanisms for each entity in the scheme to recover to a consistent state with minimal financial exposure risk.
5. Transfers become financially binding only when the hub has successfully responded to a transfer fulfilment message from the payee participant. This response is only sent when the hub has persisted the fulfilment message and its outcome to its ledger database.
6. Expiration timestamps on all financially significant API messages facilitate timely and deterministic failure path outcomes for all participants via automated retry mechanisms.
7. Given that Mojaloop schemes are intended to form parts of national financial infrastructure they must do as much as possible, given reasonable cost constraints, to avoid loss of data in the event of a failure.
8. Failures in hardware and software components are to be expected, even in the highest quality components available. Best practice suggests these failures should be anticipated and planned for in the design of the hub with a view to avoiding data loss.
9. Participants need timely confidence in the status of financial transactions across the scheme in order to minimise exposure risk and deliver excellent customer experiences.








