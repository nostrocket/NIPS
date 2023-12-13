# Rockets
A Rocket is a self-organising unorganization that exists to solve a problem. 

Rocket state exists as multiple event chains which can be compiled to produce the current state of the Rocket.

Consensus over Rocket state is based on **Votepower** which is defined in the [Nostrocket Unprotocol](#).

## Creating a new Rocket
A Rocket begins with a Rocket Ignition Event which serves as the anchor point for all activity related to the Rocket.
      e.tags.push(["metadata", rocketToPublish.Name, "name"]);
      if (rocketToPublish.ProblemID) {
        e.tags.push(["e", rocketToPublish.ProblemID, "problem"]);
      }
      if (rocketToPublish.MeritMode) {
        e.tags.push(["metadata", rocketToPublish.MeritMode, "meritmode"]);
      }
      if (rocketToPublish.Mission) {
        e.tags.push(["metadata", rocketToPublish.Mission, "mission"]);
      }
      for (let url of rocketToPublish.Repositories) {
        e.tags.push(["metadata", url.toString(), "repository"]);
      }
      if (existing) {
        e.tags.push(["e", existing.UID, "rocket"]);
      }
      e.tags.push(["e", nostrocketIgnitionEvent, "parent"]);
### Rocket Ignition Event 🔂
* `.Kind` 1031
* `.Tags`
	* [MUST]: `ignitionTag`
	* [MUST]: `metadata` with the marker `name`. This is the Name of the Rocket (`MUST be unique`; `length < 21`; `/^\w+$/`)
	* [OPTIONAL]: `metadata` with the marker `meritmode` to indicate if this Rocket is `dictator` || `pleb` mode.
	* [OPTIONAL]: `metadata` with the marker `mission` to include a Mission text.
	* [MUST]: `metadata` with the marker `repository` to include a code repository. Use multiple to include more than one.
	* [SHOULD]: `e` with the marker `problem` pointing to a `kind 1971` Problem ANCHOR
	* [OPTIONAL]:`e` pointer to a parent Rocket with the label `parent`6

##### Client Validation
`n` MUST be unique && `length < 21` && `/^\w+$/`.  
IF a Problem anchor is tagged, the Problem MUST be created by the same pubkey.   
The pubkey MUST exist in the parent Rocket's Identity Tree if specified.

## Modifying a Rocket
The Rocket creator or a maintainer MAY modify a Rocket 
* `.Kind` 1031
* `.Tags`
	* [🚀MUST]`e` pointer to a Rocket Ignition Event of `kind 1031` with the marker `rocket`, this is the rocket to modify.
	* [🚀MUST]`e` pointer to the Nostrocket anchor event with the marker `root`.
	* ALl other tags as above.



