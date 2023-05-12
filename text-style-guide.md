# Text Style Guide

## Sovryn terminology

* The name of the project is written in title-case as "Sovryn" (not "SOVRYN", even though that’s what it looks like in the logo)
* The name of the Zero protocol is “Zero” (not “ZERO”)
* The name of the Mynt protocol is “Mynt” (not “MYNT” or “Sovryn Mynt”)
* The plural of SIP (Sovryn Improvement Proposal) is “SIPs”
  * “SIP proposals” would be redundant (“Sovryn Improvement Proposal proposals”)
* The term for users who hold SOV is “SOV holder”
  * Related: the generic term for someone who holds a token is “tokenholder”, a variation on the word “shareholder”
* The term for users who stake SOV in the Bitocracy governance contract is “Sovryn staker” or “SOV staker” (or just “staker” if the context is obvious)
* The term for users who provide liquidity to the Sovryn AMM is “liquidity provider” or “LP" for short
  * The term used to describe an LP’s loss in purchasing power relative to what their purchasing power would be had they held their assets rather than deposit them into an AMM pool is “divergence loss” (not “impermanent loss”, as has been euphemistically popularized elsewhere)
* The term for users who provide liquidity to the Sovryn lending pool is “lender”
* The term for users who provide liquidity to the Zero stability pool is “stability pool depositor”

## Blockchain terminology

* The name of the blockchain network that Sovryn smart contracts are built on is “Rootstock”
  * The name of Rootstock’s native asset is Smart Bitcoin. At the Rootstock protocol level, the token symbol of Smart Bitcoin is RBTC. RBTC is a version of BTC that has been bridged to Rootstock using the Powpeg protocol.
  * In the user interface, RBTC is simply called BTC. Where clarification is needed, for example where BTC is discussed in the context of multiple different blockchains, the blockchain that BTC is used on should be referenced e.g. “Rootstock BTC” or “bitcoin mainchain BTC”.
* The name of the blockchain that BTC is originally mined on is “bitcoin”. In cases where it is ambiguous which blockchain is being referred to, the bitcoin blockchain can be referred to as the “bitcoin mainchain”.
  * The name of bitcoin’s native asset is also bitcoin. The token symbol of bitcoin is BTC. In cases where the similarities of these names may cause ambiguity, use “bitcoin” to refer to the network and “BTC” to refer to the asset.
  * In the user interface, BTC is called BTC regardless of what chain that BTC is used on. For example, RBTC is called BTC. Where clarification is needed, for example where BTC is discussed in the context of multiple different blockchains, the blockchain that BTC is used on should be referenced e.g. “Rootstock BTC” or “bitcoin mainchain BTC”.
* The word "blockchain" is not capitalized unless it is a proper noun part of something's name e.g. "Blockchain Inc."
  * The word “blockchain” is a one word (not “block chain”) [concrete noun](https://medium.com/@ryanshea/blockchain-terminology-a-grammar-usage-guide-ff54c95167bb) and should always be preceded by either a possessive noun, “a”, or “the” e.g. “bitcoin’s blockchain”, “a blockchain”, or “the blockchain”
* Web3 is always capitalized and spelled as one word
  * Using “web3” or “Web 3.0” would be incorrect
* The abbreviation of “decentralized application” is “dapp”. Not “DApp”, “dApp”, or “Ðapp”
  * There is an ongoing debate over whether the term “decentralized application” is useful to begin with, at least in user-facing communications. In light of this, we should generally refer to the software we are building as an “app” or “web app” rather than a “dapp”.
* The term “transaction ID” should be abbreviated to “TXID” in the user interface. The TXID refers to the unique identifier of a transaction (which at a technical level is a hash of the transaction’s contents).

## General

* Be succinct
  * But also, specificity is better than ambiguity. Remember that there is real money on the line.
* Sufficiently inform the user about what they are doing, what the result of their action will be, and what has happened after the action is complete
  * In error messages, whenever possible, be specific about what went wrong and either imply or explicitly say what the user must do to correct the error. If we don’t know what went wrong, link to external documentation about how to troubleshoot further or get in touch for personal support.
* Avoid jargon where possible. Where avoiding jargon is not possible, clarify in text, using a tooltip, and/or by linking to external documentation
* Use sentence casing everywhere
  * Single-sentence descriptions should not use a period at the end
* Numbers should not have trailing zeroes e.g. use 0.1 BTC not 0.1000 BTC, use 3 BTC not 3.0 BTC
* Always use an Oxford comma (“Note that a, b, and c are...”)
* Use American English spelling of a word when the spelling varies by locale
* Lists should always be in alphabetical, numerical, or chronological order, unless there’s a good reason for them not to be
* The format to use for dates should be either YYYY-MM-DD if numeric (e.g 2020-12-31) or Month D(st)(th)(D, YYYY) if spelled out (e.g. January 1st or December 31, 2020)
  * Related: all times should be communicated in HH:MM:SS UTC and specified as such to avoid the confusion of “Daylight Savings Time”, which is inconsistently implemented across the world (even [within the same country](https://en.wikipedia.org/wiki/Daylight\_saving\_time\_in\_the\_United\_States#Local\_DST\_observance)) and is [generally a scourge on humanity](https://www.inc.com/jessica-stillman/experts-to-public-daylight-savings-time-is-a-434m-problem-we-could-easily-fix.html). An exception would be if there is an event being held locally in a specific time zone and there's no need to specify a time zone because all attendees will be in that time zone. In this case, the local time zone does not need to be specified unless the event is also being livestreamed so that people worldwide can watch online; here, both the local time zone and UTC time should be specified e.g. “17:00 EDT / 21:00 UTC”.
* Bullet points:
  * Use a period (full stop) if a bullet point contains multiple sentences
  * Use no punctuation after bullet points that are fragment sentences and do not complete the stem
  * Use no punctuation after bullet points that only contain one word e.g. My favorite cryptoassets are:
    * BTC
    * SOV
  * Use all complete sentences or all fragments, but not a mixture

## Number formatting

* For each token there'll be a configuration whether the full amount should be truncated at four (4) decimal places (e.g. show up to 0.0001) or at eight (8) decimal places (e.g. show up to 0.00000001)
  * For BTC, truncate after 8 decimals, for all other assets, truncate after 4
* If the amount has been truncated, add a tilde \~ before the number to give a visual cue that this is an approximation (e.g. \~1.23456789) and round the number down at the terminal decimal place (e.g. 1.728399346 becomes \~1.72839934)
