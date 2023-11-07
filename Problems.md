# Problem Tree
🚀🍌 Definitions are located in the Nostrocket NIP index.

## Logging a New Problem
* `.Kind` 1971
* `.Tags`
	* [🚀MUST]`e` pointer to the Nostrocket root event
	* [MUST]`text` tag with the marker `sentence`: contains a once sentence explanation of the problem in `<=` 100 characters
	* [SHOULD]`text` tag with the marker `paragrah`: contains a once paragraph explanation of the problem in `<=` 280 characters
	* [MAY]`text` tag with the marker `page`: contains a detailed explanation of the problem in approximately one page
	* [SHOULD]`e` pointer to the parent of this problem with the label `parent`, MAY incude multiple parents in additional tags
	* [🚀SHOULD]`e` pointer to a Rocket to get Maintainers from (Nostrocket Rocket used by default if none specified)
	* [SHOULD]`tip` the current Bitcoin tip `<height>:<hash>`
	* [SHOULD] current lifecycle status of the problem and the pubkey that changed the status (if applicable): `["status", "<open || claimed || patched || closed">, "<pubkey>"`.
	* [MAY] `labels` tag with a list of event IDs to label and sort/filter problems by, e.g. a programming language, difficulty level, rocket, other problems, etc. 
* `.Content` empty.

## Modifying a Problem

To modify a Problem, the problem creator or a Maintainer MUST publish another `Kind 1971` event with the modifications included. Clients SHOULD render the latest event published by a valid pubkey (creator or Maintainer).
* `.Tags`
	* [MUST] `e` pointer to the Problem this event modifies, with the marker `problem`
	* [SHOULD] `e` pointer to the latest known Problem modification event (if any), with the marker `previous`.
* `.Content` [OPTIONAL] An explanation of why this is being modified. If included, this should be displayed as a comment.


## Problem Lifecycle
To signal that a problem is being worked on, a Participant SHOULD publish a NIP25 reaction in response to the Problem, with the 💪 emoji in the content. This SHOULD include an `e` tag of the Problem, AND an `e` tag of the latest known modification event with the marker `previous`.

To abandon a previously claimed Problem and signal that this problem is no longer being worked on, the current claiment SHOULD publish a NIP25 reaction with the 😓 emoji in the content.

To signal that a problem has been solved and the solution is ready to be verified, the person solving the problem should react with 👌 and also follow up with a comment if any explanation is requried.

Upon witnessing a lifecycle change, the creator of the problem or a Maintainer SHOULD modify the problem to reflect the new status.

After claiming a problem, the Participant SHOULD produce a patch or other solution within 432 blocks. If no solution is forthcoming within this time limit, a Maintainer MAY free the problem by modifying it to set the status to `open` such that other particpants can claim it.

If a Participant repeatedly claims problems and fails to deliver a solution and does not provide a reasonable explanation they SHOULD be asked to self correct, and removed from the Identity Tree if they are unable to do so.