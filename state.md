# Nostrocket VM paradigm

## Consensus 
Nostrocket consensus is based on votepower, which is a simple but nuanced quantification of a participant's "skin in the game". 
 
## State transitions, consensus rules, and extensibility of the execution environment
Nostrocket is not a Blockchain, and the problem that Nostrocket solves is completely different to the problem that Bitcoin and the EVM solve. Nostrocket is not intended to be used for money or as "global computer". However, the best way to explain how nostrocket state works is to compare it to the approaches taken by Bitcoin and Ethereum.
 
A state change request is a Nostr event that complies with the Nostrocket protocol. A valid state change request is one that complies with the protocol in the context of the current machine state.
 
To avoid language theoretic security vulnerabilities, state change requests are explicit, atomic, and do not invoke Turing complete execution. However, Nostrocket is able to solve the same problems that are solved by a Turing complete environment such as the EVM - Nostrocket can do whatever a smart contract can do, without offering a Turing complete smart contract execution environment.
 
### To understand how this works, let's examine two different Blockchain paradigms.

#### Bitcoin
**Bitcoin** handles the halting problem through limitations on state transitions - the different states that Bitcoin can enter after handling a valid transaction is finite. In order to handle novel types of state or execution, the Bitcoin VM must be modified, and Satoshi explicitly designed the system such that these modifications would be impossible without near-unanimous agreement by all participants in the network.

#### The EVM
**The EVM** handles the halting problem by using a fee consumption model that restricts the quantity of opcodes that may be executed cumulatively in a single block. This allows turing completeness while also providing halting guarantees. The major benefit here is that we don't need to modify the consensus rules to implement novel machine states as the fundamental machine state space is unbounded.

#### Nostrocket
**Nostrocket** takes the Bitcoin approach in that there's a finite set of possible states the machine can enter when handling a valid state change request. However the execution environment itself is fluid and can be modified by participants with protocol rules at the social layer, allowing an unbounded state space like the EVM, but handling the halting problem the same way Bitcoin does (only turing incomplete operations are possible). 


Participants with Votepower, and the set of Maintainers, are able to modify the execution environment itself according to the protocol rules. This happens in a fluid and dynamic manner, and allows the Nostrocket VM to provide any functionality that "smart contracts" provide when deployed to the EVM.

Users can select from a market of different "authorities" or "Rockets" (votepower roots), fork an existing one, or create an entirely new one. Rockets can also inherit partitions of dynamic state from other Rockets, allowing composability, remixing, etc.
