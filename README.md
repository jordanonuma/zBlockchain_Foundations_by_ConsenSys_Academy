# Final: Decentralized Price Feeds by First Niching Down to Proof of Stake Tokens
### What is the business case, area, or topic that this use case applies to?

This set of smart contracts’ goal is to provide a metric for Proof of Stake token security by properly incentivizing a continuous, decentralized set of markets to quantify the value of the staking token.  

### What problem are you trying to solve? What is the value created by solving this problem?

Staking tokens are becoming increasingly used in design mechanism for dApp security. Security is gained by incurring a financial penalty to the user for deviant behavior larger than any financial reward possible, where deviant behavior is defined by the protocol.

Financial penalties are made by slashing a fraction, or in some cases the full amount, of collateral the user staked. Currently, this loss is either denominated in: 1) other tokens; 2) some fiat amount using an exchange API aggregator such as coinmarketcap.com; or 3) some combination of (1) and (2).

The problem with each of these methods are detailed as follows:
- Method (1) may not have a denomination of much relevance to the audience. As an exaggerated example, if you’re asked 0.1 units of unobtainiumorz for 1 ETH, this would be a slightly humorous joke but difficult sell for many reasons.
- Method (2) uses centralized sources that necessarily need to be trusted. Exchange agreggators such as coinmarketcap.com are used heavily, and because owners of these sites have privileged write access to these feeds (often for good maintenance reasons) these also become a central point of failure and security vulnerability subject to possible corruption from within or without.

### How will a blockchain be applied to this use case? Which component pieces will be utilized? Will the blockchain used be public, private, or consortium and why?

Immutable smart contracts on the public Ethereum blockchain can be used to trustlessly create decentralized price feeds for any token. Taking the hypothetical token STAKE as an example, the decentralized price feed will include an aggregate price for STAKE in terms of other tokens (e.g. Tokens B, C, and D) where:

1) The more prices in terms of other tokens, or sub-feeds, the stabler the aggregate feed (less chance of manipulation by an adversary); and
2) Tokens B, C, and D can be any ERC-20 token such as DAI, ETH, and MKR. Eventually as fiat on-ramps become more available, these dominations can also be related directly, or indirectly, in terms of fiat (e.g. USD, YEN, KRW, and so on).

The public Ethereum blockchain is necessary because public auctions are required for each sub-feed. Each STAKE sub-feed will give a price for STAKE as a function of its denominated token as shown in its token pair (e.g. token pair STAKE:B [read as the price of token STAKE in terms of token B], token pair STAKE:C [read as the price of token STAKE in terms of token C], and so on up to however many tokens STAKE price will be denominated in).

**Case: STAKE in terms of DAI**  
The STAKE:DAI sub-feed’s procedure will be as follows:  

1) Prime the sub-feed by purchasing STAKE tokens and sending to the Auction 1 contract.
2) Auction 1 will sell a certain amount, _x_, STAKE tokens for DAI over a pre-defined time period, _t_. (The highest price of the auction, $\gamma$ DAI for every 1 STAKE, is noted).
3) Auction 2 will then take a certain amount, _y_, DAI from Auction 1 and sell over the same pre-defined time period, _t_. (The highest price of the auction, $\lambda$ STAKE for every 1 DAI, is noted).
4) Since $\gamma$ produces the maximum price of STAKE and 1/($\lambda$) produces the minimum price of STAKE, the sub-feed's price for STAKE is taken as the average of the two:

&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; _Price.SubFeed_ = 0.5 * ($\gamma$ + 1/($\lambda$))  

To make clearer with concrete numbers:  

**Auction 1 (Auctioning 1 STAKE token)**  
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; **DAI** &nbsp; &nbsp; &nbsp; **STAKE**  
**Round 1**	&nbsp; &nbsp; 1 &nbsp; &nbsp; &nbsp; &nbsp;	&nbsp; &nbsp; &nbsp;1  
**Round 2**	&nbsp; &nbsp; 1.5 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1  
**Round 3**	&nbsp; &nbsp; 1.7 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1  
**Round 4**	&nbsp; &nbsp; 2	&nbsp; &nbsp; &nbsp; &nbsp;	&nbsp; &nbsp; &nbsp;1   

$\gamma$ = 2 DAI/STAKE (read as the “price of 1 STAKE token is 2 DAI tokens.”)  

**Auction 2 (Auctioning 1 DAI token)**    
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; **STAKE** &nbsp; &nbsp; **DAI**  
**Round 1**	&nbsp; &nbsp; 0.5	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;1  
**Round 2**	&nbsp; &nbsp; 1	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1  
**Round 3**	&nbsp; &nbsp; 3	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1  
**Round 4**	&nbsp; &nbsp; 4	&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; 1  

$\lambda$ = 4 STAKE/DAI  (read as the “price of 1 DAI token is 4 STAKE tokens.”)  

&emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; _Price.SubFeed_ = 0.5 * ($\gamma$ + 1/($\lambda$)) = 0.5 * (2  + ¼) = 1.125 DAI/STAKE  

This is read as the “STAKE:DAI sub-feed’s price for every 1 STAKE token is 1.125 DAI tokens.”    

Each sub-feed will run one auction at at a time, but all sub-feeds can run simultaneously. For three (3) STAKE sub-feeds as an example, for optimum price decentralization, all 3 sub-feeds (STAKE:B, STAKE:C, and STAKE:D) will be running at any given time. STAKE:B, STAKE: C, and STAKE:D will each only have one auction running.  

On a time series sketch this might look like:  

^  
|  
| &nbsp; &nbsp; STAKE:B  
| &nbsp; &nbsp; Auction 1 | Auction 2 | Auction 3 | Auction 4  
|    
| &nbsp; &nbsp; STAKE:C  
| &nbsp; &nbsp; Auction 1 | Auction 2 | Auction 3 | Auction 4  
|    
| &nbsp; &nbsp; STAKE:D  
| &nbsp; &nbsp; Auction 1 | Auction 2 | Auction 3 | Auction 4  
-------------------------------------------------------------> time  

Lastly, the total final STAKE price feed will equal to the average of all sub-feeds’ prices every two (2) auctions.  

### How might a token be used for this use case? Why is it needed, and what benefit does it bring to the project? If a custom token isn't necessary, explain why.  

The goal of the decentralized price feed is to quantify the price of all tokens (staking tokens focused on for this case) relative to other tokens. The more liquid the auctions are, the better for giving accurate prices.

An additional token isn’t necessary to setup these auctions, but an additional custom token could be useful for setting up initial liquidity either by using fundraising capital to purchase other sub-tokens or even by setting up altogether new sub-feed token pairs such as ETH:LIQUID, MKR:LIQUID, and DAI:LIQUID. If easy fiat on and off ramps are provided using token LIQUID, the price feed will benefit from increased auction participation.  

### Describe some other details and factors that are worth considering in this use case. Are there any difficulties or challenges that may have to be solved? Are there any weaknesses you could see that could be exploited? Feel free to ask your own question here, and provide an answer, if those questions aren't helpful.

Rigorously defining the auction framework is key to encouraging honest auction participation. Possible weaknesses that could allow an adversary to exploit the price feed raises the questions:

- What is a good amount of STAKE tokens, _x_, to buy to prime the sub-feed in Auction 1?
- What is a good amount of DAI tokens, _y_, to start Auction 2?
- What is the correct pre-defined time period, _t_, to run the auctions?
- Is it better to have a dynamic time period that adjusts based on other factors such as how fast the previous auctions are sold out (indicating previous price increments were too small) or how slow the previous auctions are sold out (indicating previous price increments were too large)?

Sub-feed variables _x_, _y_, and _t_ are very important to get correct*, as it will become valuable to exploit a decentralized price feed that becomes widely used. Similar to when ETH becomes a staking token in Casper, each of the tokens that have a decentralized price feed will see an increase in supply and demand pressure. Arbitraging with smaller, less dynamic exchanges will become profitable, and supply becomes squeezed when a certain fraction of the tokens get quasi-“locked up” to participate in the continuous, decentralized sub-feed auctions open to all. Demand (buy) pressure of each token will also increase as the tokens gain a quantified value by means of an additional way for token holders (setting the price floor via the auctions) and arbitragers to make money by participating in this decentralized price feed.  

\*A counterargument to some or all of these questions is it may be preferable to have no definite number hard coded and have the market decide (for example in Bitcoin, users decide whether transactions have acceptable finality based on how many confirmations they require).  

__________________________________________________________________________________________________________________________  
Credit goes largely to the Augur team and its community members currently discussing a similar mechanism for the REP token. However I believe extending decentralized price feeds for all tokens (including ones not used in Proof of Stake such as Non-Fungible, ERC721 tokens) with many sub-feeds to make each price feed, and thus overall pricing system, more resilient from attack is a worthy project if only as a common good that floats all the boats in the cryptocurrency landscape.   
