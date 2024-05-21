# Problem
GitHub recently began banning any newly created accounts that attempted to send a pull request to Nostrocket. They are also moving very clearly in a direction that leads to KYCing contributors. 

It's become obvious that storing canonical git repositories on centralised infrastructure is no longer a viable approach for open source projects and this is especially true if you're working on freedom tech or the soon-to-be-regulated machine learning field. 

In any case, centralised git infrastructure is not going to work for Nostrocket, so we will need to solve this problem somehow.

The ideal flow would be something like this:
1. When creating a new Rocket, the user is offered the choice to also create a public facing bare git repository. The creator of the Rocket, and pubkeys in the Rocket's Maintainer Tree shall have push access (auth is by pubkey).
2. When a participant claims a problem to begin work on it, a temporary fork of the Rocket's repository is created. This is a public bare repo that only the participant can push to. The participant pushes commits as required. 
3. Once the particpant is satisfied that they have solved the problem, they create a pull request (directly in the problem tracker). This automatically squashes the commits into a single commit and copies the problem title into the commit message.
4. Anyone in the Maintainer Tree can merge the commit into master, once merged, the participant's fork is automatically deleted. 

## Requirements
* interaction with repos using schnorr public/private keypairs ONLY, no usernames/passwords/ssh keys.
* Fully compatible with existing git tooling (with addition of a remote helper or something for auth


### Patches over nostr is not the solution
My first attempt at integrating Git and nostr was to use nostr as the transport layer - patches over nostr. This is the approach I keep seeing other people take as well. But we can already do that with email, and there's a reason we usually don't. Nostr also adds the problem of event size limits, so special relays are required.

It's also a really bad idea to store patches as nostr events and use this as the canonical way to interact with a "repo"; this is a very heavy linked list that you need to rebuild any time you want to interact with the current state. Core has 38,000+ individual patches, fetching and parsing every event every time you want to clone the repo is not fun. This is why git doesn't work that way internally.

The "patches over nostr" and "repos as patches on nostr" approach is also going to be basically useless in terms of allowing people to build github type clients that are able to take full advantage of nostr to interact with "git" repositories.

### Git objects as events is not the solution
My next approach to Snub was to put git internals **directly** onto nostr as individual events, effectively storing canonical public bare git repositories on relays. After spending a week angrily implementing this, I realised it's also a dumb idea because git itself already does this far more effectively and efficiently than a relay ever will.


## Possible Solution
### SERVER: 
Implement NIP98 Authentication for public repos. Could be done with a reverse proxy, or using PAM on Gitea or something.

### CLIENT: 
Implement NIP98 Signing as a [remote helper](https://git-scm.com/docs/gitremote-helpers) or hook.

### EVENTS:
#### Repository Announcement
* `.Kind` 30617
* `.Tags`
	* [SHOULD, RAW]`d` tag of the initial commit hash
	* [SHOULD, DRAFT]`name` tag specifying the name of the repository
	* [OPTIONAL, DRAFT]`description` tag specifying a short description of the repository.
	* [OPTIONAL, RAW]`fork` tag indicating the node at which this fork diverges from upstream.
	* [OPTIONAL, RAW]`maintainers` tag containing an array of pubkys to be considered as maintainers. These pubkeys are treated by the client as having permission to replace any events with the same `d` tag if they are newer. Forks by maintainers that are used for pull requests should use the `fork` tag to differentiate them from upstream.
	* [OPTIONAL, DRAFT]`e` tag of a `kind 1031` rocket ignition event, with the marker `rocket`, to specify a Rocket from which to take the list of Maintainers and Problems/Issues.
	* [OPTIONAL, DRAFT]`web` tag pointing to a URL where the repo can be viewed with a browser.
	* [MUST, DRAFT]`git` tag pointing to a URL which provides a standard git API for clone, push, etc.
	* [OPTIONAL, DRAFT]`patches` pointing to a relay that accepts patches as events. Not specifying this means that the Maintainers only accept pull requests and not patches.
* `.Content` empty.

#### Pull Requests
* `.Kind` 1623
* `.Tags`
	* [MUST, DRAFT]`commit` the commit hash
	* [OPTIONAL, DRAFT]`web` tag pointing to a URL where the repo can be viewed with a browser.
	* [MUST, DRAFT]`git` tag pointing to a URL which provides a standard git API that can be pulled from.
	* [OPTIONAL, DRAFT]`p` tags of maintainers to notify them.
	* [MUST, DRAFT]`a` tag pointing to the base repository using the original creator's pubkey.

#### Patches
Maintainers may accept patches and manually apply them to create a commit.
* `.Kind` 1617
* `.Tags`
	* [MUST, DRAFT]`base` the commit hash that this patch is based on
	* [OPTIONAL, DRAFT]`p` tags of maintainers to notify them.
	* [MUST, DRAFT]`a` tag pointing to the base repository using the original creator's pubkey.
