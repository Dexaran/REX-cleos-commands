REX mechanics

To receive the rewards token owners have to deposit their EOS to the REX market and purchase rex in exchange for a specified amount of EOS (hereinafter, REX — resource exchange, rex — token). The rate used is 1:10,000. [deposit] [buyrex]

Rex cannot be transferred from one account to another since such an option is not provided by the platform. Generally speaking, rex is a token of accountability, the only exchange possibility there is, is between REX and the account owner.

As long as rex is in rex_balance, the user receives a proportional share of all the profits accumulated by REX over a period of time, i.e. if you hold 50% of all the rex tokens for a day and REX accumulates 100 EOS of profit from resource sales, you are to receive rex equivalent of 50 EOS (50%) as a reward.

Sources:

https://medium.com/@bytemaster/proposal-for-eos-resource-renting-rent-distribution-9afe8fb3883a

https://medium.com/@eoscafeblock/what-rex-means-for-token-holders-238375dea397

https://github.com/EOSIO/eosio.contracts/issues/117

https://github.com/EOSIO/eosio.contracts/tree/v1.6.0-rc2/contracts/eosio.system
Commands

Note: <AMOUNT> is displayed in the format 10.0000 REX or EOS

deposit

Action deposit, if called for the first time, creates a record rex_fund for the user (see info tables rexfund) and records the balance of the transferred amount in EOS. All the subsequent deposit actions add a sum to rex_fund balance .

cleos push action eosio deposit ‘{“owner”:”<YOUR ACC>”,”amount”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

withdraw

Action withdraw allows the user to withdraw EOS from rex_fund back to the inline account.

cleos push action eosio withdraw ‘{“owner”:”<YOUR ACC>”,”amount”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

updaterex

Action updaterex updates user’s vote stake to the current value of his rex balance.

cleos push action eosio updaterex ‘{“owner”:”<YOUR ACC>”}’ -p <YOUR ACC>

buyrex

Action buyrex allows the user to buy rex in exchange for a specified amount of EOS. The amount of received rex is calculated in such a way that the ratio total_lendable / total_rex has the same value before and after executing the action. To run the action buyrex the user has to vote for at least 21 BP’s or through a proxy. The EOS tokens used for the purchase are retrieved from the user’s rex_fund.

cleos push action eosio buyrex ‘{“from”:”<YOUR ACC>”,”amount”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

sellrex

Action sellrex allows the user to sell rex, EOS is returned to rex_fund. If total_unlent contains sufficient amount of EOS tokens, the sale occurs immediately. Otherwise, the sale order is added to the queue (rexqueue). The price of the order is determined at the time of processing but not when the order is placed.

The profit from the sale of rex is added to the user rex_fund.

cleos push action eosio sellrex ‘{“from”:”<YOUR ACC>”,”rex”:”<AMOUNT> REX”}’ -p <YOUR ACC>

rexexec

cleos push action eosio rexexec ‘{“user”:”<YOUR ACC>”,”max”:”<uint16>”}’ -p <YOUR ACC>

The user can rent CPU and Net on behalf of the receiver account by using actions rentcpu and rentnet in exchange for a payment of a specified amount of EOS. These actions create a record rex_loan in tables cpuloan and netloan respectively. The amount of EOS tokens added to CPU and Net resources is calculated based on the payment considering current market price defined by the Bancor algorithm.

The calculated total_staked is moved from total_unlent to total_lent and the payment is added to total_rent. Once the loan is created, the payment is added to the table rexpool total_lendable and total_unlent, consequently bringing up the rex price and increasing the amount of EOS available for rent. The loan is effective for 30 days. After this, the tokens (total_staked) are deducted from the receiver’s resources. total_staked is moved back from total_lent to total_unlent while total_lent is updated in accordance with the Bancor formula.

rentnet

cleos push action eosio rentnet ‘{“from”:”<YOUR ACC>”,”receiver”:”<RECEIVER ACC>”,”loan_payment”:”<AMOUNT> EOS”,”loan_fund”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

rentcpu

cleos push action eosio rentcpu ‘{“from”:”<YOUR ACC>”,”receiver”:”<RECEIVER ACC>”,”loan_payment”:”<AMOUNT> EOS”,”loan_fund”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

unstaketorex

Allows to buy rex with staked tokens. The amount of rex in exchange for from_net + from_cpu is calculated in the same way as with buyrex.

cleos push action eosio unstaketorex ‘{“owner”:”<YOUR ACC>”,”receiver”:”<RECEIVER ACC>”,”from_net”:”<AMOUNT> EOS”,”from_cpu”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

Upon the expiration of the loan terms, the loan is prolonged if there is enough funds in the balance, otherwise the loan is closed and any remaining balance returned to the user.

If the loan is prolonged, total_staked is recalculated considering the current market price and the user’s resource limits are updated accordingly. REX pool balances are also updated.

The loan owner can fund the loan using fundcpuloan or fundnetloan. The owner can also withdraw the remainder using defcpuloan or defnetloan (the field ‘balance’ is changed in this case, see table cpuloan).

fundnetloan

cleos push action eosio fundnetloan ‘{“from”:”<YOUR ACC>”,”loan_num”:”<uint64>”,”payment”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

fundcpuloan

cleos push action eosio fundcpuloan ‘{“from”:”<YOUR ACC>”,”loan_num”:”<uint64>”,”payment”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

defnetloan

cleos push action eosio defnetloan ‘{“from”:”<YOUR ACC>”,”loan_num”:”<uint64>”,”amount”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

defcpuloan

cleos push action eosio defcpuloan ‘{“from”:”<YOUR ACC>”,”loan_num”:”<uint64>”,”amount”:”<AMOUNT> EOS”}’ -p <YOUR ACC>

consolidate

The action allows the user to combine balances into one bucket with a maturity period of 4 days (see info tables rexbal).

cleos push action eosio consolidate ‘{“owner”:”<YOUR ACC>”}’ -p <YOUR ACC>

cnclrexorder

Using the action cnclrexorder, the user can cancel the rex sale order that was added to the queue. It can be done at any time before the order is filled.

cleos push action eosio cnclrexorder ‘{“owner”:”<YOUR ACC>”}’ -p <YOUR ACC>

closerex

Deletes user’s rex_balance and rex_fund and releases RAM. The action cannot be executed if the user’s balance is higher than 0.

cleos push action eosio closerex ‘{“owner”:”<YOUR ACC>”}’ -p <YOUR ACC>

mvtosavings

Moves a specified amount of rex into the savings bucket. rex savings bucket never matures. In order for it to be sold, it has to be moved explicitly out of that bucket. Then the moved amount will have the regular maturity period of 4 days starting from the end of the day.

cleos push action eosio mvtosavings ‘{“owner”:”<YOUR ACC>”,”rex”:”<AMOUNT> REX”}’ -p <YOUR ACC>

mvfrsavings

Moves a specified amount of rex out of the savings bucket. The moved amount will have the regular rex maturity period of 4 days.

cleos push action eosio mvfrsavings ‘{“owner”:”<YOUR ACC>”,”rex”:”<AMOUNT> REX”}’ -p <YOUR ACC>

After executing the mvtosavings and mvfrsaving commands the changes can be viewed in rex_maturities in the table rexbal.
Info tables

cpuloan

cleos get table eosio eosio cpuloan — index 3 — key-type name -L <YOUR ACC> -U <YOUR ACC>
“version”: 0,
“from”: “”,
“receiver”: “”,
“payment”: “xx.xxxx EOS”,
“balance”: “x.xxxx EOS”,
“total_staked”: “xx.xxxx EOS”,
“loan_num”: xx,
“expiration”: “2019–01–21T14:24:04.500”

netloan

cleos get table eosio eosio netloan — index 3 — key-type name -L <YOUR ACC> -U <YOUR ACC>

delband

сleos get table eosio eosio delband

rexqueue

cleos get table eosio eosio rexqueue

rexbal

cleos get table eosio eosio rexbal -L <YOUR ACC> -U <YOUR ACC>
“rows”: [{
“version”: 0,
“owner”: “”,
“vote_stake”: “xx.xxxx EOS”,
“rex_balance”: “xxxxxx.xxxx rex”,
“matured_rex”: xxxxxxxxx,
“rex_maturities”: [{
“first”: “2019–01–08T00:00:00”,
“second”: 2722988285
},
{
“first”: “2019–01–09T00:00:00”,
“second”: 114185696
}
]

Purchased rex cannot be sold earlier than 4 days after the purchase. After several purchases the tokens are accumulated in separate period buckets represented by the field rex_marurities in rex_balance (contains amounts that can be sold in less than 1 day, 2 days, etc.). The rex that can be sold immediately is stored in the field matured_rex of rex_balance.

consolidate is the action that allows the user to combine balances into one bucket with the overall maturity period of 4 days that starts from end of day.

rexfund

cleos get table eosio eosio rexfund -L <YOUR ACC> -U <YOUR ACC>

{
“rows”: [{
“version”: 0,
“owner”: “”,
“balance”: “xx.xxxx EOS”
}
],
“more”: false
}

rexpool

total_rex is a total amount of rex, it increases or decreases when rex is bought or sold.

total_lendable — is a general EOS value. At any moment in time the price of rex is calculated as follows: total_lendable / total_rex. Loan payments for CPU and Net are added to total_lendable, thus increasing the rex price and providing more EOS tokens for rent.

total_unlent is a part of total_lendable, that is available for rent, while total_lent is a part that is tied to unpaid loans. Therefore, total_lendable = total_unlent + total_lent.

total_rent — is a sum of all the unredeemed payments on the CPU and NET loans.

total_rent and total_unlent are bound together with a market maker (Bancor algorithm).

cleos get table eosio eosio rexpool

{
“rows”: [{
“version”: 0,
“total_lent”: “xxxxxx.xxxx EOS”,
“total_unlent”: “xxxxxx.xxxx EOS”,
“total_rent”: “xxxxxx.xxxx EOS”,
“total_lendable”: “xxxxxx.xxxx EOS”,
“total_rex”: “xxxxxxxxx.xxxx rex”,
“namebid_proceeds”: “x.xxxx EOS”,
“loan_num”: xxx
}
],
“more”: false
}
