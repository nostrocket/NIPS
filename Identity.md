# Identity Tree:
* Permanyms MUST be unique within the tree.
* Rockets can have multiple trees which are used for permissions over certain functions, for example people in the Maintainer tree can merge patches.
* Single non-replaceable events are used rather than a replaceable list such that there is a historical record of action taken within the Identity Tree.

## Adding people to the tree
* `.Kind` 15171032
* `.Tags`
  * `d` tag is the pubkey of the person being added, self if this is the first event in the Tree
    * if this is the first event of the Tree, the pubkey MUST match the creator of the Rocket and be signed by them
  * `n` tag is the permanym of the person being added (from their kind0)
  * `a` tag is `15171031:<pubkey of rocket creator>:<d tag of Rocket || pubkey of Tree creator>`
  * `e` tag event ID of a Kind 15171032 event adding the signer to the tree (except for the event adding the Tree creator)
  * `e` tag event ID of Nostrocket ignition event
* `.Content` if this event is created by the rocket creator, and it is the first Identity Tree event for the rocket, it SHOULD include a description of the purpose of the tree in the Content field.

## Removing people from the tree
This event is used to remove a bad acting branch from the tree.
A bad actor can be identified by observing the behavior of the accounts in their branch of the tree.
In the case of spammers (for example), the person who added the spammer SHOULD remove them from the tree.
If they do not, or if they add more accounts that turn out to be spammers, then it's likely that they are in fact conducting a sybil attack in which case the person who invited them SHOULD remove them from the tree.
* `.Kind` 15171033
* `.Tags`
  * `e` tag of the 15171032 event that added the bad actor to the tree
  * `a` tag is `15171031:<pubkey of rocket creator>:<d tag of Rocket>`
  * `e` tag event ID of Nostrocket ignition event with the label `root`
* `.Content` MUST include the reason for removal, and SHOULD include evidence (event IDs of spam messages for example) 

## Detached Identity Trees
A detached Identity Tree is one that is not associated with a Rocket. It could be used for public group chats for example.

### 15171032:
* do not include the `a` tag
* do not include the `e` tag of the Nostrocket ignition event
* `n` becomes optional
* for all events other than the very first addition (the Tree creator adding themselves), it MUST include an `e` tag pointing to the event that adds the Tree creator.

### 15171033
* do not include the `a` tag
* do not include the `e` tag of the Nostrocket ignition event
* MUST include `e` tag pointing to the event that adds the Tree creator

### Client Behavior
Clients build a representation of the Identity Tree in local memory by subscribing to kind 15171032 and 15171033 events and parsing these events through the following logic:
1. Take the set of all 15171032 events that are signed by the creator of the Rocket (or the creator of the Tree if detached)
2. Remove IDs from the set if they are included in any 15171033 event signed by same pubkey.
3. Sort the resulting set by event creation time.
4. For each event, validate that no previous event contains the same `n` tag (permanym) or `d` tag (pubkey of the person being added). Remove events from the set if they do not pass this test.
5. Take all `d` tags from the resulting set, and repeat steps 1-5 using these pubkeys.
