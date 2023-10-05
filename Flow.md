# Consensus
### Before Login, or login with pubkey that has no votepower
We follow Consensus events to rebuild the current state of Nostrocket.  
`var currentState` is our current local Nostrocket state tip.
`mempool` all `state change requests` that are potentially valid but not included in the current state.

1. Subscribe to all events that have a `e` tag pointing to the ignition event with label `root`.
2. Store all events in `cache`; Filter `consensus` => `kind == 15172008` && `pubkey` is one we know has votepower (starting with the ignition account).
3. Find first `consensus` event: `previous` will be set to the ignition event.
4. Fetch (from `cache`) the `state change request` event tagged in the `consensus` event's `e` tag (labeled `request`). This is the event that caused a state change.
5. Process the `state change event` by validating it against the Unprotocol, and updating local `currentState` to include the new state.

A fork happens when we have `consensus` events tagging different `request` events at the same height in the consensus event chain. In this situation, we follow the fork with the most `votepower`. 

We SHOULD also check for checkpoint OP_RETURNs from pubkeys with votepower.

Continute iterating to keep up to date with the current state.

### Upon Login

1. Broadcast a `kind 20081` (ephemeral) event every 30 seconds to let others know we are online with a Nostrocket client, and subscribe to these events so that we know who is online too.
2. Iterate over the consensus chain from above. If we have `votepower > 0` at any point, and we are NOT a signatory to a consensus event in the chain, produce one.
3. If we are currently the highest votepower online, we SHOULD lead the consensus process by handling `state change request` events currently in `mempool`:
	5. Process the `state change event` by validating it against the Unprotocol. We **do not** update the current state during this process, insetad, we publish a new `consensus` event which will be picked up by the process above and that will add the event to our current state.

