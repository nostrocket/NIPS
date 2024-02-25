# Nostrocket VM paradigm

### Consensus
Nostrocket consensus is based on [votepower](#), which is a simple but nuanced quantification of a participant's "skin in the game".    
### State transitions, consensus rules, and extensibility of the execution environment
Nostrocket is not a Blockchain and is not intended to be used as money or a "smart contract" platform, however, the best way to explain how nostrocket state works is to compare it to the approaches taken by Bitcoin and Ethereum.   
**Bitcoin** handles the halting problem through limitations on state transitions - the different states that Bitcoin can enter after handling a valid transaction is finite. 

In order to handle novel types of state or execution, the Bitcoin VM must be modified, but Bitcoin is explicitly designed such that these modifications are impossible without near-unanimous agreement between the time-bounded rolling-set of participants who recieve Bitcoin as a form of payment.

**The EVM** handles the halting problem with a fee consumption model that limits opcode execution. 

This allows Turing pseudo-completeness (with halting guarantees). 

The major benefit is we don't need to modify the consensus rules to implement novel functionality. 

However the language complexity required precludes full recognition of valid or expected inputs, hence the (easily predicted) DAO hack etc.
 
A **Nostrocket** state change request is a Nostr event that complies with the protocol in the context of the current state.   
 
To avoid [language theoretic security vulnerabilities](http://langsec.org/), state change requests are explicit, atomic, and do not invoke Turing complete execution (the same model as Bitcoin). 

However, Nostrocket has the same level of flexibility as a Turing complete environment such as the EVM. Instead of this flexibility being invoked by "transactions", it is prestated in the codebase that a user executes (leveraging their web of trust) and invoked by explicit and atomic state change requests.

