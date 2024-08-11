## Consensus over State
Consensus should be used as sparingly as possible and only for the most critical aspects of the Nostrocket Unprotocol:  

* Merit Requests and voting
* Payments (payment for products => compensation and dividends)

HEAD pointer (helper) - replaceable event by anyone with votepower
Consensus events are like tree merges but by Votepower instead of Maintainers

Protocol Flow:  
1. Participant publishes a valid state change event.  
2. Anyone with `Votepower > 0` merges the event into their consensus chain and updates their HEAD to point to the latest consensus event.
3. Anyone else can immediately get the same state by following the same chain. If we have votepower we produce our own consensus chain while doing this.
4. If we see mismatched consensus chains, we follow the one with the most votepower.


### Consensus Event
* `.Kind` 2008
* `.Tags`
	* [MUST]: `ignitionTag`
	* [SHOULD]: `e` pointer to the `kind 1031` Rocket Ignition event with the label `rocket`
	* [MUST]: `e` pointer to the state change event being witnessed, with the label `request`
	* [SHOULD] `request` embedded JSON encoded event of the state change request
	* [MUST]: `e` pointer to the last valid consensus event published by this pubkey, with the label `previous`
	* [SHOULD]: `bitcoin` `<current bitcoin height>:<height of this event in this pubkey's consensus chain>`

### Consensus HEAD Event
This event stops us from producing extra consensus events while catching up to the current state.
* `.Kind` 12008

We do not publish a consensus event unless we have a HEAD event of our own. Our first HEAD event should be a duplicate of someone else's (the first time after increasing our lead time to > 0)

- Rocket Anchor
  - Merit Requests
  	- Merit Votes
  - 

# Consensus State (requires consensus events for every state change)
- Merit Table (requests, votes, payments)
- Protocol Rules
- Identity Tree
- Maintainer Tree

Instead of validating identity tree inclusion as a normal client operation, only do it for creating a consensus event. When rebuilding state, don't validate. This solves problem of changes to the tree. If votepower created a consesnsus event we assume they at least were in the tree at that time.

