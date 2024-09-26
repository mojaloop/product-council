# Mojaloop Feature List

**DRAFT VERSION - SUBJECT TO REVIEW**

**25th September 2024**  
Authors: Paul Makin, James Bush  
Version: Draft 0.4  
# About Mojaloop
Mojaloop is open source instant payments software that interconnects disparate financial institutions in a manner that promotes financial inclusion and provides robust risk management. It is available for use by any body that wishes to use it to implement and operate a payments scheme.

These are the core functions that Mojaloop provides to connected financial institutions:   

||||
|--------------|--------------|--------------|
|<img align="centre" width="300" src="./alias.png"> &nbsp; &nbsp; |<img align="centre" width="300" src="./clearing.png">|<img align="centre" width="300" src="./settlement.png">|
| Payee address or **alias resolution**, ensuring that the correct payee account is reliably identified |**Clearing** of payments end to end, with robust measures that remove any element of doubt about the success of a transaction|**Settlement** of cleared transactions between financial institutions using a model agreed between those institutions, and according to a predefined schedule.|

&nbsp;

|Alias Resolution|Clearing|Settlement|
|:--------------:|:--------------:|:--------------:|
| Payee address or **alias resolution**, ensuring that the correct payee account is reliably identified |**Clearing** of payments end to end, with robust measures that remove any element of doubt about the success of a transaction|**Settlement** of cleared transactions between financial institutions using a model agreed between those institutions, and according to a predefined schedule.|

&nbsp;

These core functions are supported by some [unique characteristics](./transactions.md#Unique-Transaction-Characteristics), which together make Mojaloop a low cost, inclusive instant payments system:  


1. **The Agreement of Terms, or Quotation,** phase of a transaction, which allows two DFSPs to agree that a transaction *can* take place *before* it is committed.
2. **End to End Non-Repudiation** guarantees that a transaction will only be committed if *both* the Payer and the Payee DFSPs accept that it is, so neither party can repudiate the transaction. 
3. **The PISP API is made available through the Mojaloop Hub,** not by individual DFSPs. Consequently a fintech can integrate with the Hub and immediately be connected to **all** connected DFSPs.

**Note** In Mojaloop terms, a DFSP - or Digital Financial Service Provider - is a generic term for any financial institution, of any size or status, that is able to transact digitally. It applies equally to the largest international bank and the smallest Microfinance Institution or mobile wallet operator. "DFSP" is used throughout this document.   

&nbsp;
# Purpose of This Document  
This document catalogues the features of Mojaloop, independent of implementation.  It is intended to both inform potential adopters of the features they can expect and (where appropriate) how those features can be expected to function, and to inform developers of the features they must implement in order for their efforts to be accepted as an official instance of Mojaloop.  
 
The Mojaloop Foundation (MLF) defines an implementation as being an official instance of Mojaloop if it implements all of the features of Mojaloop, without exception, and they pass the standard set of Mojaloop tests.   

# Feature List
This feature list covers the following aspects of Mojaloop:
- [**Transactions**](./transactions.md), describing the use cases supported, the APIs, how a Mojaloop transaction proceeds, and the aspects of a Mojaloop transaction that make it uniquely suited to the implementation of an inclusive instant payments service.
- [**Risk Management**](./risk.md), setting out the measures taken to ensure that no DFSP participating in a Mojaloop scheme is exposed to any counterparty risk, and that the integrity of the scheme as a whole is protected.
- [**Product and Operational Features**](./product.md), such as portals for user and service management, and the configuration and operation of a Mojaloop Hub.     
- [**Engineering Principles**](./engineering.md), such as algorithmic adherence to the Mojaloop specification, code quality, security practices, scalability and performance patterns (amongst others).  
- [**Invariants**](./invariants.md), setting out the development and operational principles to which any Mojaloop implementation must adhere.   

&nbsp;
# Scope
This feature list is subject to amendment as the Mojaloop ecosystem continues to develop.   









