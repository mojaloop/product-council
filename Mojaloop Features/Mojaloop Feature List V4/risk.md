# Risk Management
A key aspect of the operation of a payment scheme that is built around a Mojaloop hub is the management of risk between transacting parties, who will themselves have different appetites for risk. The principles applied are:
1. All Participants (FSPs) are required to deposit liquidity with the Scheme operator. This liquidity can only be withdrawn, in whole or in part, from the Scheme with the agreement of the Scheme Operator.
2. A transaction will only be cleared (during the Quotation phase) if there is sufficient liquidity available to cover it, as measured against the liquidity balance and the DFSP's current Position (the net total of already cleared transactions, as either payer or payee).
3. A cleared transaction will be added to the DFSP's Position.
4. During settlement, a negative Position will be debited from the Liquidity balance and transferred to the settlement partner for distribution to the creditors; a positive Position will be credited to the Liquidity balance by the settlement partner.
5. A DFSP is expected to manage their liquidity, adding to it if it drops to a level where anticipated transaction values will result in failed transactions, or withdrawing some (on application to the scheme operator) if the value is too high.
6. Where the settlement partner is not available 24/7, a DFSP can deposit extra balance to their liquidity account, for example to cover anticipated transactions over a holiday period. A DFSP can manage this extra balance using a Net Debit Cap (NDC), which might be used for example to limit the use of liquidity to the levels that are expected on a particular day, in order to ensure that the DFSP can continue to trade throughout the whole of the holiday period. The NDC is used in tandem with the liquidity balance in the authorisation of transactions during the Quotation phase.   









