# NIP-1592: Identity Tree
###### STATUS: DRAFT

## Language

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in RFC 2119.

The key words "RAW", "DRAFT", "STABLE", "DEPRECATED", and "RETIRED" in this document are to be interpreted as described in NIP-1962.

Lines marked 🚀 are only applicable when when consuming this NIP attached to Nostrocket.  
Lines marked 🍌 are only applicable when consuming this NIP independently from Nostrocket.

## Starting a new Identity Tree

* `.Kind` 1592
* `.Tags`
	* [MUST]🚀`e` pointer to a Rocket Ignition Event of `kind 1031` with the marker `rocket`.
	* [MUST]🚀`e` pointer to the Nostrocket ignition event with the marker `root`
	* [MUST] at least ONE `p` tag with the hex pubkey of someone to add to the Identity Tree,  MAY also include a marker for the credentials of this addition, for example `participant` or `maintainer`. As this is the first event, it SHOULD include the pubkey of the creator of this event to add themselves to the Tree.
	* [SHOULD] current Bitcoin height and hash
* `.Content` [🚀SHOULD] be an empty string. [🍌MAY] a description of the purpose of this Identity Tree.
* `.Pubkey` [🚀MUST] be the same pubkey as that which created the Rocket in the first `e` tag.

## Adding an Identity to the Tree
* `.Kind` 1592
* `.Tags`
	* [🍌MUST]`d` pointer to the root of this Identity Tree (a `kind 1592` event) with the marker `root`.
	* [🚀MUST]`e` pointer to a Rocket Ignition Event of `kind 15171031` with the marker `rocket`.
	* [🚀MUST]`e` pointer to the Nostrocket anchor event with the marker `root`
	* [SHOULD] at least ONE `p` tag with the hex pubkey of someone to add to the Identity Tree,  MAY also include a marker for the credentials of this addition, for example `participant` or `maintainer`.
	* [SHOULD] current Bitcoin height and hash
* `.Content` SHOULD be an empty string.

## Removing an Identity from the Tree

Identity Tree events are considered client side replaceable. They are not relay replaceable because we require a historical record of additions and removals from the Tree.

To remove someone that you have added, simply publish an event as above, but do not include their `p` tag.

This enables identity trees to be reconstructed by retrieving the latest events (similar to how replaceable events work), while retaining the ability to see the history of the tree.

## Client Behavior [RAW]
1. Clients SHOULD request all `kind 1592` events published by the creator of a Rocket and tagged with the Rocket. Take the earliest event (but created after the time the Rocket event was published. This is the first event in the Identity Tree. Add each pubkey in the event's `p` tags to the local state of the Identity Tree, along with the block height and timestamp of the event. Maintain a counter of how many pubkeys have been added so far, and increment this with each addition, and associate this "account number" with the added pubkey.

2. Subscribe to all `kind 1517` events from each pubkey we just added, and repeat the process.

3. If a pubkey is already in the tree, ignore.

4. If a removal is detected, remove that pubkey. Do not decrement the counter from step 1, but instead use a removal counter in addition to this.