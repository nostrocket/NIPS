# Identity Tree:
‼️🔷🚀🍌🔂 Definitions are located in the Nostrocket NIP index.

An Identity Tree is a graph of identities that makes Sybil attacks more expensive to conduct than it is to mitigate them.

* **Permanyms** in an Identity Tree are both unique and permanent within the Tree. They should usually be the same as the username in the user's `kind 0` event.

* Rockets can have multiple trees which are used for permissions over certain functions, for example people in the Maintainer tree can merge patches.

* Identity Trees are composed of event chains that are compiled into a current state. We use this rather than using replaceable list events so that there is a transparent historical record of action taken within the Identity Tree. 

## Creating a new Tree
### Tree Ignition Event 🔂
* `.Kind` 15171032
* `.Tags`
	* 🚀`a` pointer to a Rocket `15171031:<pubkey of rocket creator>:<d tag of Rocket>`
	* 🚀`e` pointer to the Nostrocket ignition event with the label `root`
* `.Content` SHOULD include a description of the purpose of the Tree.

#### Client Validation
* 🚀Signer MUST be the creator of the Rocket tagged in the `a` tag.

### Tree Metadata Event (replaceable)
##### This is optional
* `.Kind` 31032
* `.Tags`
	* ‼️`d` random hash
	* ‼️`e` pointer to a Tree Ignition Event
	* 🍌🔷`fee` `<event ID of state change event where this takes effect from>:<amount in sats>:<LUD16>:<Authorized zap reciept issuer pubkey>` Multiple allowed.
	* 🚀🔷`fee` amount in sats (paid to merit holders of the Rocket)
	* 🔷`checkpoint` the event ID of a state change event to be used as a checkpoint in the event chain. Multiple allowed.
	* 🔷`maintainer` a pubkey that clients SHOULD follow for Tree Metadata Events in addition to the Tree Ignition Event creator.

#### Client Validation
MUST be Signed by same pubkey as the Tree Ignition Event or one of the `maintainer` pubkeys (can be nested).

## Requesting to join a Tree
### Tree Join Event
The purpose of this event is to allow people to specifiy a **Permanym** to be used with this Tree instead of using their current `Kind 0` data. Permanyms MUST be unique within the Tree and cannot be modified once a pubkey has been added to the Tree. 

This event is OPTIONAL, members MAY be added to a Tree without this event. 

* `.Kind` 15171033
* `.Tags`
	* ‼️`n` a string of length `=< 20` containing the requested Permanym.
	* ‼️`e` pointer to a `kind 15171032` Tree Ignition Event
	
## Adding people to the tree
### Tree Merge Event 🔂
* `.Kind` 15171034
* `.Tags`
	* ‼️`e` pointer to a `kind 15171032` Tree Ignition Event
  	* ‼️`p` pubkey of the person being added to the Tree
  	* 🚀‼️🍌🔷`n` Exactly ONE of the following:
  		*  a embedded `kind 15171033` event containing an explicitely requested Permanym
  		*  an embedded `kind 0` event to use as the Permanym
  	* ‼️`e` pointer to the `kind 15171034` event adding the signer of this event to the Tree unless this event is signed by the creator of the `kind 15171032` root of the tree and the `p` tag is their own pubkey.
  	* `e` pointer to the Nostrocket ignition event with the label `root`
  	* 🚀‼️🍌🔷`o` pointer to the latest valid state change event 🔂 in the tree
  	* 🔷`payment` an embedded zap reciept 
  	

#### Client Validation
* The Permanym embedded within the `n` tag MUST be unique within the Tree.
* If `fee` on the `31032` Metadata event is defined somewhere in the event chain before this event, payment is mandatory and must be validated.

## Removing people from the tree
### Tree Purge Event 🔂
A bad actor can be identified by observing the behavior of the accounts in their branch of the tree.
In the case of spammers (for example), the person who added the spammer SHOULD remove them from the tree.
If they do not, or if they add more accounts that turn out to be spammers, then it's likely that they are in fact conducting a sybil attack in which case the person who invited them SHOULD remove them from the tree.

* `.Kind` 15171035
* `.Tags`
	* ‼️`e` pointer to a `kind 15171032` Tree Ignition Event
	* ‼️`e` pointer to the `kind 15171034` Tree Merge event that added the bad actor to the Tree
	* 🚀`a` `15171031:<pubkey of rocket creator>:<d tag of Rocket>`
	* 🚀`e` pointer to the Nostrocket ignition event with the label `root`
	* 🚀‼️🍌🔷`o` pointer to the latest valid state change event 🔂 in the tree
* `.Content` MUST include the reason for removal, and SHOULD include evidence (event IDs of spam messages for example) 

#### Client Validation
Signer MUST be the creator of the `kind 15171034` Tree Merge event that added the bad actor to the Tree

## Reference Client Behavior
Clients build a representation of the Identity Tree in local memory by subscribing to Tree events and parsing these events through the following logic:

#### Create Objects
##### somewhere to temporarily store all events from the relay
`var setOfEvents []nostr.Event`
##### stores for our current Tree state:  
`var treeState map[pubkey]Tree Merge Event`  
`var blacklist []pubkey` `//users SHOULD be presented a warning if trying to add a blacklisted pubkey`
`var permanyms map[string]null`
`var paymentRequired int`
`var zapReceiptPubkey string`
##### A pointer to the latest state change event
`var lastStateChangeEventID string`

#### Build the Current State
0. Start with a `kind 15171032` Tree Ignition Event of the Tree you want to find the current state of. 
1. Take the set of all events that tag the `kind 15171032` Tree Ignition Event and store them in `setOfEvents`. 
2. Find the first Tree Merge event by taking `setOfEvents` and finding a Tree Merge Event for which the `o` tag `==` Tree Ignition Event ID, AND is signed by the same pubkey. This SHOULD be an event from the Tree creator adding themselves to it.
3. If the Tree Merge event is valid according to this NIP, update our local state:
	1. Create entry in `treeState`:
		* `k` = pubkey in the Tree Merge event `p` tag 
		* `v` = the Tree Merge event. 
	2. Set `lastStateChangeEventID` to the value we just put into `k`
	3. Add the permanym to `permanyms`
4. Take the `setOfEvents` and find the subset that are State Change Events (Tree Merge, or Tree Purge), with the `o` tag set to `lastStateChangeEventID` AND is signed by a pubkey that exists in `treeState`. If this is a Tree Merge Event repeat from step 4. If this is a Tree Purge event:
	5. Check that the referenced Tree Merge Event exists in `treeState` and is signed by the same pubkey as this Purge event. 
	6. Remove from `permanyms` and `treeState`. Add to `blacklist`. Update `lastStateChangeEventID`.
7. Start again from step 5

#### Handling Forks
Identity Trees are constructed as an event chain, we handle forks in the chain by processing all of them and finding the longest one. A fee can be charged to make it expensive to produce long forks.

If at any point there are `>1` State Change events 🔂 found with the same `o` tag, make a copy of our current state for each event and process them independently of each other and then discard the shorter ones. 

Tree creators and Maintainers SHOULD update the Tree Metadata Event with checkpoints. Clients MAY take the latest checkpoint and work backwards.

#### Handling Fees
If a `fee` is added to the Metadata event, it includes the ID of the state change event from which the fee becomes active. This allows fees to be changed without breaking the validation of existing Merge events.

We validate zap receipts came from the correct pubkey, and that the zap receipt hasn't been used previously, and was created within x minutes of the Merge event.

## OP_RETURN
🚀 If an Identity Tree is anchored in a Rocket (i.e. **not** 🍌detached), then the Merkle root of the Identity Tree SHOULD be included in the Nostrocket Merkle Tree such that it is included in OP_RETURNs.

