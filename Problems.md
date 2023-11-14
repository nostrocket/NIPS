# NIP-1971: Problem Tracking
###### STATUS: **DRAFT**

## Language

The key words “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” in this document are to be interpreted as described in RFC 2119.

The key words "RAW", "DRAFT", "STABLE", "DEPRECATED", and "RETIRED" in this document are to be interpreted as described in NIP-1962.

Lines marked 🚀 are only applicable when when consuming this NIP attached to Nostrocket.  
Lines marked 🍌 are only applicable when consuming this NIP independently from Nostrocket.


## Logging a New Problem
* `.Kind` 1971
* `.Tags`
	* [🚀MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [🍌SHOULD, RAW]`e` pointer to the top level problem in a non-Nostrocket Problem Tree, with the marker `root`
	* [MUST, DRAFT]`text` tag with the marker `tldr`: contains a once sentence explanation of the problem in `<=` 100 characters
	* [SHOULD, DRAFT]`text` tag with the marker `paragraph`: contains a one paragraph explanation of the problem in `<=` 280 characters
	* [OPTIONAL, DRAFT]`text` tag with the marker `page`: contains a detailed explanation of the problem, ideally in something like one page. Markdown is allowed.
	* [SHOULD, DRAFT]`e` pointer to the parent of this problem with the marker `parent`, MAY incude multiple parents by including additional tags
	* [🚀SHOULD, DRAFT]`e` pointer to a Rocket from which to get Maintainers (Nostrocket Rocket is used by default if none specified) 
	* [SHOULD, RAW]`tip` tag with the current Bitcoin tip when this event is published `<height>:<hash>`
	* [SHOULD, DRAFT] current lifecycle status of the problem and the pubkey that requested a change to this status (if applicable): `["status", "<open || claimed || patched || closed">, "<pubkey>", "<witnessed height (bitcoin)>"]`.
	* [OPTIONAL, RAW] `labels` tag with a list of event IDs to label and sort/filter problems by, e.g. a programming language, difficulty level, rocket, other problems, etc.
	* [MUST, RAW] IF this is a new Problem (not modifying an existing problem), an empty `new` tag MUST be included. 
* `.Content` empty.

## Modifying a Problem

To modify a Problem, the problem creator or a Maintainer MUST publish another `Kind 1971` event with the modifications included. Clients SHOULD render the latest event published by a valid pubkey (creator or Maintainer).

* `.Tags`
	* [MUST, DRAFT] `e` pointer to the Problem this event modifies, with the marker `problem`
	* [MUST, RAW] `e` pointer to the latest known Problem modification event (if any), with the marker `previous`.
* `.Content` [OPTIONAL, RAW] An explanation of why this is being modified. If included, this should be displayed as a comment.

## Problem Lifecycle
To signal that a Participant wants to work on a Problem, they SHOULD reply to the problem event:

* `.Kind` 1972
* `.Tags`
	* [🚀MUST, STABLE]`e` pointer to the Nostrocket root event, with the marker `root`
	* [🍌SHOULD, RAW]`e` pointer to the top level problem in a non-Nostrocket Problem Tree, with the marker `root`
	* [MUST, STABLE]`e` pointer to the `1971` problem event with the marker `problem`.
	* [MUST, DRAFT]`["status", "<open || claimed || patched || closed>"`  

After claiming a problem, the Participant SHOULD produce a patch or other solution within 432 blocks. If no solution is forthcoming within this time limit, the problem will be automatically freed for others to solve.

A Maintainer MAY provide extra time in response to a request by the Participant who claimed the proble. This is done by publishing a new `kind 1971` event with a modified claim timestamp.

To abandon a previously claimed Problem and signal that this problem is no longer being worked on, the current claiment SHOULD publish another `1972` event with the status `open`

To signal that a problem has been solved and the solution is ready to be verified, the person solving the problem SHOULD publish another `1972` event with the status `patched`, and follow up with a comment linking to a merged or proposed patch, and provide any explanation if required.

Upon witnessing any valid lifecycle change, clients should display the new status immediately, and a Maintainer SHOULD solidify this new status with a new `1971` event modifying the problem to reflect the new status, time of the change, and pubkey of the requestor.

If a Participant repeatedly claims problems and fails to deliver a solution or request a time extension and does not provide a reasonable explanation, they SHOULD be asked to self correct, and removed from the Identity Tree if they are unable to do so.