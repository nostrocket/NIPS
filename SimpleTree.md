# Simplified Identity Tree
* `.Kind` 31009
* `.Tags`
	* [MUST] `d` tag of 🚀Rocket Ignition Event ID `||` 🍌 Identity Tree Creation Event
	* [MUST]🚀`e` pointer to a Rocket Ignition Event of `kind 15171031` with the label `rocket`.
	* [MUST]🚀`e` pointer to the Nostrocket ignition event with the label `root`
	* [SHOULD] at least ONE `p` tag with the hex pubkey of someone to add to the Identity Tree,  MAY also include a label for the name of the Identity Tree.
* `.Content` SHOULD be an empty string.

## Client Behavior
Clients SHOULD request the latest `kind 31009` event published by the creator of a Rocket and tagged with the Rocket, to find the initial set of Identities. 

Iteratively repeat this for all pubkeys found.