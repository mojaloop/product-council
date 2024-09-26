
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
1. **The Agreement of Terms, or Quotation,** phase of a transaction, which allows two DFSPs to agree that a transaction can take place *before* it is committed. This supports some of the most complex aspects of transactions between differing types of Participant; a Payee DFSP can verify that the customer's account can receive the payment, that it hasn't been suspended, or the payment won't breach transaction or balance limits. If that's all OK, then the Payee DFSP can accept the transaction, subject to any fees that it will charge, and the Hub can add its own charges. Only if the Payer DFSP, and the Payer him/herself, agree to those charges will the transaction then take place. This removes the uncertainty, and all but guarantees that the transaction will succeed, even before it happens.
2. **End to End Non-Repudiation** in the Transfer phase of the transaction guarantees that the transaction will only be committed if *both* the Payer and the Payee DFSPs accept that it is; and, once committed there is cryptographic evidence of this, and neither party can repudiate the transaction. This obviates the need for reconciliation, drives down on the level of disputed transactions, and so substantially reduces costs for all participants.
3. **The PISP API is made available through the Mojaloop Hub,** not by individual Participants. Consequently a fintech can integrate with the Hub and immediately be connected to all connected DFSPs, rather than needing to complete an API integration with the all individually. This substantially reduces costs and increases reliability for fintechs and their customers.






