# Merits

## Requesting Merits for Work Done
People who have solved a problem are eligible to request merits for the value of the work they have done.

* `.Kind` 1602
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the problem that this request is in repsonse to, with the label `problem`
	* [MUST, DRAFT]`e` pointer to a Rocket that the merits should be created by, with the label `rocket`
	* [MUST, DRAFT]`["amount", "<amount in sats>"]` the value of the merit request in sats
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MAY, DRAFT]`["onchain", <Bitcoin Address>` if the Contributor wants Bitcoin instead of Merits
	* [MAY, DRAFT]`["zap", <LUD16>, <pubkey trusted for zap receipts>]` if the Contributor wants Bitcoin instead of Merits
* `.Content` empty.

Consensus events can then be used to add this request to a consensus chain, indicating that the signer believes the block height specified is correct. Block heights are used for timing the vote on merit requests and so it is important to reach consensus over the height a merit request was created at.

## Voting to approve or reject merit requests
People who have merits are able to vote to approve or reject new merit requests. Votes cannot be modified once created.

* `.Kind` 1603
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request that this is voting on, with the label `merit`
	* [MUST, DRAFT]`e` pointer to a Rocket that the merit was created by, with the label `rocket`
	* [MUST, DRAFT]`["vote", <"ratify" | "blackball">` direction of the vote
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
* `.Content` empty.

Consensus events can then be used to add this vote to a consensus chain in the usual manner, causing a HARD state change that updates the vote tally.

## Sponsoring a Contributor
A person who is in the Identity Tree MAY sponsor a Contributor by paying them for their work. In doing so, the sponsor recieves the Contributor's Merits.

To do this, the sponsor should make the payment and publish an event (at the same time).

* `.Kind` 1604
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request they want to sponsor
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MUST, DRAFT]`["payment", <emedded zap reciept event | bitcoin tx hash>]`
* `.Content` empty.

Upon witnessing and validating this event from any pubkey in the identity tree, the client MUST NOT continue to list this Merit Request for sale. If there's proof of fuckery, the pubkey should be removed from the Identity Tree.

Consensus events can then be used to add this proof to a consensus chain after validating the payment (depth of 6 blocks for an on-chain transaction). 

