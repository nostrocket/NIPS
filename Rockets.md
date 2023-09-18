# Rockets
A Rocket is a self-organising unorganization that exists to solve a problem. 

Rocket state exists as multiple event chains which can be compiled to produce the current state of the Rocket.

Consensus over Rocket state is based on **Votepower** which is defined in the [Nostrocket Unprotocol](#).

🚀‼️🍌🔹
## Creating a new Rocket
A Rocket begins with a Rocket Ignition Event which serves as the anchor point for all activity related to the Rocket.

### Rocket Ignition Event 🔂
* `.Kind` 15171031
* `.Tags`
	* 🔹`e` pointer to a parent Rocket
	* ‼️ `e` pointer to the Nostrocket ignition event with the label `root`
* `.Content` SHOULD include a description of the purpose of the Tree.

#### Client Validation
* 🚀Signer MUST be the creator of the Rocket tagged in the `a` tag.




- Rocket Anchor
  - Merit Requests
  - Merit Votes

# Consensus State (requires consensus events for every state change)
- Merit Table (requests, votes, payments)
- Protocol Rules
- Identity Tree
- Maintainer Tree
- 