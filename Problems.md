# Problem Tree
‼️🚀🍌 Definitions are located in the Nostrocket NIP index.
## Problem ANCHOR Event
* `.Kind` 31107 (replaceable)
* `.Tags`
	* ‼️`d` random hash
	* ‼️`a` a pointer to the parent of this problem, MAY incude multiple parents in additional `a` tags
	* 🚀`e` pointer to the Nostrocket ignition event `["e", "1bf16cac62588cfd7e3c336b8548fa49a09627f03dbf06c7a4fee27bc01972c8", "", "root"]`
	* 🍌`m` A hex encoded pubkey of a Maintainer. Maintainers can modify problems. MAY include any number of `m` tags. If none are specified, only the creator of the problem can modify it.
 

## Problem HEAD Event
This is a pointer to the current Problem state defined in a COMMIT event.

* `.Kind` 31108 (replaceable)
* `.Tags`
	* ‼️`a` pointer to a `kind 31107` Problem Anchor.
	* ‼️`e` pointer to a `kind 311070` Commit event.
	* 🚀`e` pointer to the Nostrocket ignition event   
	
##### Client Validation
* Clients MUST verify that the author is a Maintainer on the Rocket (🚀) or Problem (🍌).
* Clients MUST use the latest HEAD event published by any pubkey that is a Maintainer on the Rocket or Problem.


## Problem COMMIT Event
This is a request to modify the current state of a Problem.

* `.Kind` 311070
* `.Tags`
	* ‼️`a` pointer to a `kind 31107` Problem Anchor.
	* ‼️`e` pointer to the `kind 311070` event that this commit is based on
		*  This MUST be excluded IF and ONLY IF there are no previous commit events for this problem 
	* MUST include at minimum ONE of the following
		* `e` pointer to a Problem TEXT event of `kind 311071`
		* `t` a list of relevant event IDs to tag this problem with, for example a snub git repo event ID, a Rocket igniton event, a label definition event. This list must be complete, as it will overwrite anything in the previous commit.
		*  `s` status tag: `open || claimed || closed `
	* 🚀`e` pointer to the Nostrocket ignition event 

##### Client Validation
Clients MUST validate
Rocket Tag: MUST be a maintainer on that rocket to merge a commit that adds the rocket to the `t` tag.

## Problem Text Event 
* `.Kind` 311071
* `.Tags`  
	* ‼️`t` the title of the Problem. MUST be plaintext of `=< 100` characters. 
	*  `s` a summary of the problem for use as a preview. MUST be plaintext of `=< 280` characters
	* `b` the body of the Problem. MAY contain markdown.
	* 🚀`e` pointer to the Nostrocket ignition event 



## Label Definition Event
e.g. a problem/solution difficulty level, a programming language, etc  

* `.Kind` 311072
* `.Tags`
	* ‼️`n` Name of the keyword. MUST be `=< 16` characters
	* `s` a definition of the label. MUST be plaintext of `=< 280` characters.
	* 🚀`e` pointer to the Nostrocket ignition event 

## FAQ
Q: How do we modify the Anchor event if the problem creator disappears?   
A: Close the problem and create a new anchor event. Create a new HEAD event pointing to the latest Commit.

---
Q:  
A: