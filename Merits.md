# Merits

## Requesting Merits for Work Done
People who have solved a problem are eligible to request merits for the value of the work they have done.

#### Kind: 1409

#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The problem that was solved|`[ "problem", "1971", 1971:<problem creator pubkey>:<kind 1971 problem tracker event d tag>, <relay hint> ]` `[ "problem", "text" <a brief plaintext description of the problem that has been solved> ]` |
|OPTIONAL|Proof of solution|`["solution", "url", <a merged pull request, url of files, etc>]` `["solution", "uri", <an image (e.g. a receipt)>]`|
|MUST|The SEC this is making a claim from|`["a", 31108:<pubkey>:<rocket d tag>]`|
|MUST|number of merits being requested|`["merits", "<int>"]`|
|OPTIONAL|The amount being requested in Sats|`["sats", <int>]`|
| SHOULD | Current block, mostly for convenience. | `[ "bitcoin", "<current bitcoin height>:<hash>" ]` |

## Voting to approve or reject merit requests
People who have votepower SHOULD indicate if the Merit Request is valid or not by publishing a vote.

#### Kind 1410
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|Tag the Rocket|`["a", "31108:<pubkey>:<dtag>"]`|
|MUST|The request being voted on|`[ "request", <ID of 1409 request> ]`|
|MUST|The direction of the vote|`["vote", <"ratify" | "blackball">]`|
| SHOULD | Current block, mostly for convenience. | `[ "bitcoin", "<current bitcoin height>:<hash>" ]` |
|OPTIONAL|Tag the author of the kind 1409 Merit Request|`["p", <pubkey>]`|
|OPTIONAL|Tag the merit request itself|`["e", <ID of 1409 event>]`|

An Approved Merit Request is one which has been ratified by `>50%` of votepower and has been blackballed by `<2%` of votepower.

## Proof of Approved Merit Request
To include an Approved Merit Request in the state of a MSB, a proof is required. This event is not intended to be published but instead stringified and embedded as a `proof_full` tag (see NIP31108).

#### Kind 1411
#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The request being voted on|`[ "request", <stringified JSON of the merit request> ]`|
|SHOULD|Votes. Add a new tag for each. |`["vote", <stringified JSON of a vote>]`|
| SHOULD | Current block, mostly for convenience. | `[ "bitcoin", "<current bitcoin height>:<hash>" ]` |

#### Verification
Clients should compute the current votepower of each pubkey included in each `vote` tag and update the state to include this Merit Request if it is ratified.

## Exchanging Approved Merit Requests for Sats
To exchange an Approved Merit Request for Sats, a Contributor SHOULD publish a kind `1605` event.
#### Kind: 1412
#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The Approved Merit Request(s) being sold, may be used multiple times to sell a batch|`[ "request", <IDthe merit request> ]`|
|SHOULD|The Rocket|`["a", 31108:<pubkey>:<rocket d tag>]`|
|MUST|The price range (in sats) that the contributor will accept|`["price", <start<int>:end<int>>]`|
| MUST | Current block | `[ "bitcoin", "<current bitcoin height>:<hash>" ]`|
|MUST|Payment address|`["onchain", <Bitcoin Address>]`|

The auction is not valid until votepower has included it in the current state, along with this event as the proof.

## Sponsoring a Contributor
A participant who wishes to support a Contributor SHOULD do so by purchasing an Approved Merit Request from the contributor.

In order to do this they must first publish the Bitcoin address they wish to pay from. 
#### Kind: 1413
#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The Rocket in which the person wishes to own merits |`["a", 31108:<pubkey>:<rocket d tag>]`|
|MUST|A Bitcoin address that funding will come from (downstream change addressess will autmotically be included by the client after the first transaction)|`["onchain", <Bitcoin Address>]`|

The Rocket's votepower should then include this event as a proof and add the Bitcoin address and npub to the Rocket's current state. All transactions at a depth of >5 that spend Bitcoin from this address to an address specified in a valid auction listing, and are of the correct amount, are considered proof that the purchase has been made successfully, and the Merits will thereafter be owned by the npub associated with the spending address.

Change addresses following this transaction will automatically be considered by the client to be a spending address associated with same npub.


A person who is in the Identity Tree MAY sponsor a Contributor by paying them for their work. In doing so, the sponsor recieves the Contributor's Merits.

To do this, the sponsor should make the payment and publish an event (at the same time).

	* [MUST, DRAFT] 
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MAY, DRAFT]`["onchain", <Bitcoin Address>` if the Contributor wants Bitcoin instead of Merits
	* [MAY, DRAFT]`["zap", <LUD16>, <pubkey trusted for zap receipts>]` if the Contributor wants Bitcoin instead of Merits

* `.Kind` 1604
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request they want to sponsor
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MUST, DRAFT]`["payment", <emedded zap reciept event | bitcoin tx hash>]`
* `.Content` empty.

Upon witnessing and validating this event from any pubkey in the identity tree, the client MUST NOT continue to list this Merit Request for sale. If there's proof of fuckery, the pubkey should be removed from the Identity Tree.

Consensus events can then be used to add this proof to a consensus chain after validating the payment (depth of 6 blocks for an on-chain transaction). 

