# Problem Tree
🚀🍌 Definitions are located in the Nostrocket NIP index.
## Problem ANCHOR Event
All data about this Problem can be found by subscribing to this event.

* `.Kind` 15171971
* `.Tags`
	* [🚀MUST]`e` pointer to the Nostrocket root event
	* [🍌MUST]`e` pointer to a problem tracker root event
 
## Problem HEAD Event
Each Maintainer of a Problem (starting with the publisher of the ANCHOR event) MAY publish what they believe to be the legitimate current state of the Problem. This is to be published as a replaceable event so that it can be updated. Clients can render the current state of a problem by finding the latest HEAD event that is published by someone the current user trusts, either directly or indirectly (by starting with the maintainer list published by the problem creator, or the list of maintainers from the associated Rocket).

* `.Kind` 31971 (replaceable)
* `.Tags`
	* [🚀MUST]`e` pointer to the Nostrocket root event
	* [MUST]`e` pointer to a `kind 15171971` ANCHOR for this Problem.
	* [MUST]`e` pointer to a `kind 15171972` COMMIT that the publisher acknowledges as the most recent legitimate state of the Problem.
	* [🚀SHOULD]`e` pointer to a Rocket to get Maintainers from (Nostrocket Rocket used by default if none specified)
	* [SHOULD]`a` pointer to the parent of this problem, MAY incude multiple parents in additional `a` tags
	* [🍌SHOULD]`p` a pubkey with the label `maintainer` is considered a Maintainer of this Problem, arbitrary number of these tags MAY be included.
	* [SHOULD]`h` the current Bitcoin tip `<height>:<hash>`
	* [SHOULD]`s`status tag copied from the commit event: `open || claimed || closed`
	
#### Client Validation
* Clients MUST verify that the author is a Maintainer on the Rocket (🚀) or Problem (🍌).
* Clients MUST use the latest HEAD event published by any pubkey that is a Maintainer on the Rocket or Problem.

## Problem COMMIT Event
This is a request to modify the current state of a Problem.

* `.Kind` 15171972
* `.Tags`
	* [🚀MUST]`e` pointer to the Nostrocket root event 
	* [MUST]`a` pointer to a `kind 15171971` Problem ANCHOR.
	* [SHOULD]`e` pointer to the `kind 15171972` event that this commit is based on with the label `previous`
		*  Excluded IF there are no previous commit events for this problem 
	* [MUST] include at minimum ONE of the following
		* `e` pointer to a Problem TEXT event of `kind 15171973` with the lable `content`
		* `e` pointer to anything relevant to this Problem. For example a Snub git repo event ID, a Rocket igniton event, a `kind 15171481` Marker event. Arbitrary numbers of this tag MAY be included. This list must be complete, as it will overwrite anything in the previous COMMIT.
		*  `s` status tag: `open || claimed || closed` //if `claimed` then the signer is the claimer

#### Maintainer/Client Validation
Rocket Tag: pubkey MUST be a maintainer on that rocket to merge a commit that adds the rocket to the `t` tag.

## Problem Text Event 
* `.Kind` 15171973
* `.Tags`  
	* [🚀MUST]`e` pointer to the Nostrocket root event 
	* [MUST]`t` with the label `title`. This is the title of the Problem. MUST be plaintext of `=< 100` characters. 
	* [SHOULD]`t`with the label `summary`. This is a summary of the problem for use as a preview. MUST be plaintext of `=< 280` characters
	* [SHOULD]`t`with the label `content`. This is the body of the Problem. MAY contain markdown.

## Marker Definition Event
e.g. a problem/solution difficulty level, a programming language, etc  

* `.Kind` 15171481
* `.Tags`
	* [🚀MUST]`e` pointer to the Nostrocket root event 
	* [MUST]`t` with the label `name`. Name of the keyword. MUST be `=< 16` characters
	* [SHOULD]`t` with the label `summary` a definition of the label. MUST be plaintext of `=< 280` characters.

