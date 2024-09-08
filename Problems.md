# NIP-1971: Problem Tracking
Kind `31971`

| RFC 2119 | Description | Spec or Example |
|---|---|---|
|MUST|Identifier|`["d", <random 256bit hex>]`|
|MUST|One sentence describing the problem, used as the title in clients|`["tldr", <up to 140 character description of the problem>]`|
|SHOULD|One paragraph describing the problem|`["para", <up to 560 charactars summarizing the problem>]`|
|OPTIONAL|One page describing the problem, MAY include markdown|`["page", <extended description of the problem>]`|
|SHOULD|The parent(s) of this problem|`["a", 31971:<pubkey>:<256bit hex>, "parent"]`|
|OPTIONAL|Rockets that this applies to|`["a", 31108:<pubkey>:<rocket name>]`|
|SHOULD|Lifecycle status|`["status", "<draft \| rfm \| big \| children \| open \| claimed \| patched \| closed">]`|
|OPTIONAL|Claim data|`["claim", <pubkey claiming it>:<bitcoin height when claimed>]`|
|SHOULD|Child status upon creation|`["child_status", <rfm \| open >]`|
|OPTIONAL|repository |`["repo", 30617:<pubkey>:<d-tag>]`|
|OPTIONAL|required skill, language, etc|`["skill", "golang"]` `["skill", "docker"]`|
|OPTIONAL|references to other problems |`["a", 31971:<pubkey>:<256bit hex>, "mention"]`|
|SHOULD|Pubkeys of maintainers, add a new tag for each, clients SHOULD copy the parent's list to begin with|`["maintainer", <pubkey>]`|
|SHOULD| Current block when publishing, mostly for convenience. | `[ "bitcoin", "<current bitcoin height>:<hash>" ]` |


Events published by maintainers and using the same `d` tag are considered as replacements (by the client, not relays) if they are newer.

Clients get maintainer lists from the event itself, if none are specified then they use the parent event list, the tagged rocket(s), tagged NIP34 repo, etc.

### Lifecycle
Problems are intended to go through a specific lifecycle. 

Problems begin as `open`. Problems that can be solved in something like 6 hours of work or less can be claimed and solved. To claim a problem, a participant MUST publish a kind 1 event in reply to the problem, with the tag `["claim"]`. Maintainers SHOULD then update the status of the problem to `claimed` and include the `claim` tag indicating which pubkey claimed it and when.

Maintainers themselves can claim a problem by simply updating the `claim` tag without publishing an extra events (but maybe they should anyway as a courtesy).

The reason problems should be claimed before working on them is to prevent duplicate work.

If the person who claimed a problem does not send a pull request or some other solution and is not responsive for a period of greater than 144 blocks, maintainers should revert the problem back to the `open` status so that it can be claimed by someone else.

When the person who claimed a problem has created a patch or other solution they should reply to the event with a kind 1 note explaining the solution or linking to a pull request etc, and include the tag `patched`.

If a problem looks to be bigger or more complicated than something that can be solved in less than 6 hours of working time, maintainers should set the status to `big` needs children because it's too big. 

If a problem has `open` children maintainers should set the status to `children`.


| Status | Valid Next States | Notes |
|---|---|---|
|`open`|`children` `claimed` `patched` `closed` ||
|`children`|`open` |Only once all children are closed|
|`big`|`children` ||
|`claimed`|`open` `patched` `closed` ||
|`patched`| `open` `claimed` `closed` ||
|`closed`| `open` |re-opening a closed problem|

