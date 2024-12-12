# Open Banking for Fintechs Workstream
This is the working space for the Open Banking for Fintechs workstream, to be used until there are outputs and documents ready to be merged into the main Mojaloop repos.

This workstream aims to provide tools to facilitate the participation of fintechs in a Mojaloop-based payments scheme, using the PISP interface. This is to be achieved through the development and adoption of a solution similar to the ITK solution for DFSPs, that is, an integration toolkit that exists within the fintech’s domain.

This workstream is complementary to the PISP workstream; whereas that workstream looks towards the Mojaloop Hub (and focusses on the interactions between the ITK and the Hub), this workstream looks outwards, towards the fintech, and provides an Open Banking-style interface.

The workstream lead is Alan Kajangwe of WiredIn.

Framing comments from Product Council session, 10th December 2024:
-	There are many Open Banking standards in existence. However consensus amongst industry experts is that those defined for Brazil (Pix) the UK are often those chosen as the model for others. This workstream will therefore focus on those as models. 
-	Many open banking standards have their roots in account aggregation (AISP), in jurisdictions where people typically have multiple bank and other FS accounts. However this is not the primary interest for the Mojaloop Community, as many of our adopters operate in jurisdictions where payment initiation (PISP) is the priority. This reality will be reflected in this workstream.
-	It was questioned why the workstream doesn't just adopt ISO 20022 for PISP/AISP - the answer being that no such schema exists. Michael Richards has been asked to explore the possibility of developing such a schema with the ISO 20022 Payments SEG; when and if such a schema is defined, the descendants of this workstream will seek to adopt it.
-	The Interscheme and FX workstreams have given the Community the capability to deploy multiple Mojaloop hubs, all interoperating/routing payments, including across borders. However, this is currently out of scope for this workstream, which will be limited to a single Mojaloop instance. The multi-scheme capacity will be left for a future workstream.