# Merits

## Requesting Merits for Work Done
People who have solved a problem are eligible to request merits for the value of the work they have done.

* `.Kind` 1818
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the problem that this request is in repsonse to
	* [MUST, DRAFT]`e` pointer to a Rocket that the merits should be created by
	* [MUST, DRAFT]`amount` the value of the merit request in sats
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MAY, DRAFT]`["on-chain", <Bitcoin Address>` if the Contributor wants Bitcoin instead of Merits
	* [MAY, DRAFT]`["zap", <LUD16>, <pubkey trusted for zap receipts>]` if the Contributor wants Bitcoin instead of Merits
* `.Content` empty.

## Voting to approve or reject merit requests
People who have merits are able to vote to approve or reject new merit requests. Votes cannot be modified once created.

* `.Kind` 1819
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request that this is voting on
	* [MUST, DRAFT]`["vote", <"approve" | "blackball">` direction of the vote
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
* `.Content` empty.

## Merit Request Status Change
Anyone with votepower MAY create a vote proof event, indicating that the merit request has been approved or blackballed in their subjective view of nostrocket state, and add this to their consensus chain using a consensus event.

* `.Kind` 1820
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request that this relates to
	* [MUST, DRAFT]`["result", <"approved" | "rejected">` direction of the vote
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MUST, DRAFT]`proof` with an embedded `kind 1819` vote event. Repeat for all relevant votes.
* `.Content` empty.

This is a HARD state change event and so it is not valid unless included in a Consensus Event within a valid consensus chain. To be certain that it cannot be reorged, we do not consider Consensus Chains to be secure unless signed by >50% of the current votepower.



## Converting Approved Merit Requests to Merits
Ideally, Merits would always be created at 1 satoshi of work = 1 merit. However a problem arises with this approach: it does not account for the change of risk over time. People contributing to a project early in its life have no idea if it will ever become a thing, their work may never pay off. People contributing after a project has already started to make profit do not have this risk. Nostrocket must be neutral and fair to all contributors whether they are the first contributor to a Rocket or if they are someone coming along a decade later when it's a stable revenue-producing endevour.

Here's my best attempt at solving this problem in the simplest possible way:

The value (in satoshi) of an approved Merit Request is the amount that the Rocket owes the contributor for their work, we call this an ROU. If the Rocket begins generating revenue (people paying to use whatever the Rocket builds), then this revenue goes towards repaying the ROUs owed to contributors in order of merit request approval. 

Once the contributors have been repaid, additional revenue is considered a form of "dividend" to be distributed to contributors based on how much work they contributed, and how much "risk" there was in contributing at that time (how likely was it that the project would be a success at the time they contributed to it?). So how do we quantify this? I can't think of a better way to deal with this than leaving it up to the market.

Contributors are able to request Merits for their work, or they can request Bitcoin. If they request Bitcoin, then their approved Merit Request will be able to be purchased by other Participants in the Rocket. The purchaser must pay the Contributor (lightning or on-chain) the amount of Satoshi claimed in the Merit Request. The amount of Merits produced is dependent on how many Blocks (Bitcoin) it takes for the approved Merit Request to be purchased:

We take the median of all sales for the last 2016 blocks as our base price (how many Merits per Sat) and multiply this by the amount of sats claimed in the Merit Request to give us the base number of Merits for this Merit Request. We then divide that by 2. This is the starting "price" for this Merit Request. Every Block, we multiply this amount by 1.02. We continue this until someone pays the Contributor the amount in their Merit Request to buy their Merits. Then, instead of the Merits (and "ROU") being added to the Contributor's pubkey, they are added to the purchaser's pubkey.

This is how we use the the market to determine the relative value of Merits to Sats. This value is a data point that Nostrocket can then use when creating new Merits.

For Merit Requests where the Contributor does not want Bitcoin but instead wants Merits, the Merits are created at the median rate of all sales for the last 2016 blocks.

### Buying Merits from Contributors
A person who is in the Identity Tree MAY pay a Contributor for their Merits, as described in the process above.

To do this, they should make the payment and publish an event (at the same time).

* `.Kind` 1821
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request that this relates to
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MUST, RAW]`amount` the amount of Merits as calculated above.
	* [MUST, DRAFT]`["proof", <emedded zap reciept event | bitcoin tx hash>]`
* `.Content` empty.

Upon witnessing and validating this event from any pubkey in the identity tree, the client MUST NOT continue to list this Merit Request for sale. If there's proof of fuckery, the pubkey should be removed from the Identity Tree.

Upon validating the payment (the zap reciept or witnessing the on-chain tx going into a block), the Contributor or anyone with votepower SHOULD produce a confirmation event to indicate that they have included this in their subjective HARD state:

* `.Kind` 1822
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request that this relates to
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MUST, DRAFT]`["payment", <embedded 1821 payment event>]`
* `.Content` empty.

This event causes a state change in clients: the purchaser's pubkey is credited with the ROU and Merits that would otherwise have been credited to the Contributor's pubkey. A data point is also created: the ratio of created Merits to Satoshi claimed in the Merit Request.