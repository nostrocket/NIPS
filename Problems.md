# Problem Tree
‼️🚀🍌 Definitions are located in the Nostrocket NIP index.
### Problem Anchor Event
* `.Kind` 31107 (replaceable)
* `.Tags`
	* ‼️`d` random hash
	* ‼️`a` pointer to the parent of this problem, 
	* 🚀`e` a `reply` to a `kind 15171031` Rocket ignition event
	* 🚀`e` the Nostrocket ignition event
		* `["e", "1bf16cac62588cfd7e3c336b8548fa49a09627f03dbf06c7a4fee27bc01972c8", "", "root"]`
	* 🍌`m` A hex encoded pubkey of a Maintainer. Maintainers can modify problems. MAY include any number of `m` tags. If none are specified, only the creator of the problem can modify it.
 

### Problem State Event
* `.Kind` 311071 (non-replaceable)
* `a` pointer to a `kind 31107` Problem Anchor.
* 
* Clients MUST verify that the author is a Maintainer on the Rocket or Problem.
* 	
	
#### Detached problem tracker
A detached Problem Tree is one that is not associated with a Rocket.

* Include these additional tags:
	* 🍌`m` A hex encoded pubkey of a Maintainer. Maintainers can modify problems. MAY include any number of `m` tags. If none are specified, only the creator of the problem can modify it.
* Do **not** include these tags:
	* `e` a pointer to a `kind 15171031` Rocket ignition event