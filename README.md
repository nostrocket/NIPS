# NIPs
The NIPs in this repository describe how Nostrocket uses events. This SHOULD NOT be considered final or up to date.

## Event Kinds
* `15171031` Rocket Ignition Event
* `31108` Rocket Metadata Event
* `15172008` Consensus Event (signed by a pubkey with Votepower)
* `31009` Identity Tree Event (list of pubkeys to include in the tree)
* `1971` Problem Tracker Event

## INDEX

* [Rockets](Rockets.md)
* [Identity Tree](SimpleTree.md)
* [Problem Tracker](Problems.md)
* [Non-binding ideas about protocol flow](Flow.md)
* [Non-binding ideas about VM architecture](state.md)
* [Original Identity Tree](Identity.md)

## Key
Most Nostrocket data structures can be used with or without Nostrocket. When Nostrocket NIPs are used without being attached to the Nostrocket economic system, we say that it is being used *detached*. 

The following icons are used to represent what data should and should not be included in Events depending on whether the NIP is being consumed as part of the Nostrocket economic system, or if it's being used *detached* from Nostrocket.

* 🚀 Indicates that this is only applicable when when operating attached to Nostrocket.  

* 🍌 Indicates that this is only applicable when used in detached mode.

`ignitionTag = ["e", "1bf16cac62588cfd7e3c336b8548fa49a09627f03dbf06c7a4fee27bc01972c8", "", "root"]`

`note1r0cketrztzx06l3uxd4c2j86fxsfvfls8klsd3aylm38hsqewtyqyp7wj7`


