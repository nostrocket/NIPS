# Nostrocket VM paradigm

### Consensus
Nostrocket consensus is based on [votepower](#), which is a simple but nuanced quantification of a participant's "skin in the game".    
 
### State transitions, consensus rules, and extensibility of the execution environment
Nostrocket is not a Blockchain, and the problem that Nostrocket solves is completely different to the problem that Bitcoin and the EVM solve. Nostrocket is not intended to be used for money or as "global computer". However, the best way to explain how nostrocket state works is to compare it to the approaches taken by Bitcoin and Ethereum.   
 
A state change request is a Nostr event that complies with the Nostrocket protocol. A valid state change request is one that complies with the protocol in the context of the current machine state.   
 
To avoid [language theoretic security vulnerabilities](http://langsec.org/), state change requests are explicit, atomic, and do not invoke Turing complete execution. However, Nostrocket is able to solve the same problems that are solved by a Turing complete environment such as the EVM - Nostrocket can do whatever a smart contract can do, without offering a Turing complete smart contract execution environment.   
 
### Comparisons

#### Bitcoin
**Bitcoin** handles the halting problem through limitations on state transitions - the different states that Bitcoin can enter after handling a valid transaction is finite. In order to handle novel types of state or execution, the Bitcoin VM must be modified, but Bitcoin is explicitly designed such that these modifications are impossible without near-unanimous agreement between the time-bounded rolling-set of participants who recieve Bitcoin as a form of payment.

#### The EVM
**The EVM** handles the halting problem with a fee consumption model that limits opcode execution. This allows Turing pseudo-completeness (with halting guarantees). The major benefit is we don't need to modify the consensus rules to implement novel functionality. However the language complexity required precludes full recognition of valid or expected inputs, hence the (easily predicted) DAO hack etc.
