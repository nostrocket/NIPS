# Merits

## Requesting Merits for Work Done
People who have solved a problem are eligible to request merits for the value of the work they have done.

#### Kind: 1602

#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The problem that was solved|`[ "problem", 1971:<problem creator pubkey>:<kind 1971 problem tracker event d tag>, <relay hint> ]`|
|MUST|The ignition event of the SEC this is making a claim from|`["e", <ID of MSB ignition event>]`|
|MUST|number of merits being requested|`["merits", "<int>"]`|
|OPTIONAL|The amount being requested in Sats|`["sats", <int>]`|
| SHOULD | Current block, mostly for convenience. | `[ "bitcoin", "<current bitcoin height>:<hash>" ]` |

## Voting to approve or reject merit requests
People who have votepower SHOULD indicate if the Merit Request is valid or not by publishing a vote.

#### Kind 1603
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The request being voted on|`[ "e", <ID of 1602 request> ]`|
|MUST|The direction of the vote|`["vote", <"ratify" | "blackball">]`|
| SHOULD | Current block, mostly for convenience. | `[ "bitcoin", "<current bitcoin height>:<hash>" ]` |

An Approved Merit Request is one which has been ratified by `>50%` of votepower and has been blackballed by `<2%` of votepower.

## Proof of Approved Merit Request
To include an Approved Merit Request in the state of a MSB, a proof is required. This event is not intended to be published but instead stringified and embedded as a `proof_full` tag (see NIP31108).

#### Kind 1604
#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The request being voted on|`[ "request", <stringified JSON of the merit request> ]`|
|SHOULD|Votes. Add a new tag for each. |`["vote", <stringified JSON of a vote>]`|
|SHOULD|The state that this applies to|`["base", <ID of a 31108 event>]`|
| SHOULD | Current block, mostly for convenience. | `[ "bitcoin", "<current bitcoin height>:<hash>" ]` |

#### Verification
Clients should compute the current votepower of each pubkey included in each `vote` tag and update the state to include this Merit Request if it is ratified.

## Exchanging Approved Merit Requests for Sats
To exchange an Approved Merit Request for Sats, a Contributor SHOULD publish a kind `1605` event.
#### Kind: 1605
#### Tags
| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|The Approved Merit Request being sold|`[ "request", <IDthe merit request> ]`|
|MUST|The ignition event of the SEC|`["e", <ID of MSB ignition event>]`|
|MUST|The price range (in sats) that the contributor will accept|`["price", <start<int>:end<int>>]`|
| MUST | Current block | `[ "bitcoin", "<current bitcoin height>:<hash>" ]`|
|MUST|Payment address|`["onchain", <Bitcoin Address>]`|




## Sponsoring a Contributor
A participant who wishes to support a Contributor SHOULD do so by purchasing an Approved Merit Request from the contributor.

In order to do this they must first publish 



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

