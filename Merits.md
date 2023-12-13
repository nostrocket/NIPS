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

## Consensus over the result of a vote
Anyone with votepower MAY create a vote proof event, indicating that the merit request has been approved or blackballed in their subjective view of nostrocket state, and add this to their consensus chain using a consensus event.

* `.Kind` 1820
* `.Tags`
	* [MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [MUST, DRAFT]`e` pointer to the merit request that this relates to
	* [MUST, DRAFT]`["result", <"approved" | "rejected">` direction of the vote
	* [SHOULD, RAW]`bitcoin` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [MUST, DRAFT]`proof` with an embedded `kind 1819` vote event. Repeat for all relevant votes.
* `.Content` empty.

This is a HARD state change event and so it is not valid unless included in a Consensus Event within a valid consensus chain.
