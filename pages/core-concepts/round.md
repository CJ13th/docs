---
description: What role does a Round play in Allo?
---

# Round

A Round is how a [Program](program.md) would distribute a set of funds. A new Round contract is created per grant distribution.

For example, if a DAO wanted to hold a quarterly vote to distribute some of its treasury among community projects, then it would create a new Round each quarter. This is where projects would apply to receive funding and be admitted into the round. It is also where community members would vote on projects and where funds would be distributed from.

<figure><img src="../.gitbook/assets/Round.png" alt=""><figcaption></figcaption></figure>

### Rounds, In-depth

Once it is created, a Round goes through three phases: application, voting, and payout. All three of these are marked by timestamps:

| Phase       | Timestamp                                                      |
| ----------- | -------------------------------------------------------------- |
| Application | after `applicationsStartTime` and before `applicationsEndTime` |
| Selection   | after `applicationsEndTime` before `roundStartTime`            |
| Voting      | after `roundStartTime` and before `roundEndTime`               |
| Payout      | after `roundEndTime`                                           |

Each of the above timestamps is set during contract creation. There are setter methods on the contract for updating these timestamps after contract creation.

The majority of the logic for a round takes place in the [Voting Strategy](voting-strategy/) and [Payout Strategy](payout-strategy/). These are customizable logic contracts that are set at contract creation.

As an example, Gitcoin recently ran three simultaneous rounds, including a [Climate Solutions](https://go.gitcoin.co/blog/announcing-the-gitcoin-alpha-round) round ([contract](https://go.gitcoin.co/blog/announcing-the-gitcoin-alpha-round), [metadata](https://cloudflare-ipfs.com/ipfs/bafkreighrvkk654o5amjlnim7mc4cf4qvofgx3yeftv2kzjama2phood4e)). The application period was open from January 6, 2023 (timestamp `1673028540`) to January 16, 2023 (timestamp `1673913600`). Voting for the round opened on January 17, 2023 (timestamp `1673956800`) and closed on January 31, 2023 (timestamp `1675209540`). The token used for voting was [DAI](https://etherscan.io/address/0x6B175474E89094C44Da98b954EedeAC495271d0F). The [Voting Strategy](voting-strategy/) used was Gitcoin's [Quadratic Funding Voting Strategy](https://etherscan.io/address/0xBaA2f652c7a2228F2e161A615B8a55b359a0a2c8#code). The Payout Strategy used was Gitcoin's [Merkle Payout Strategy](https://etherscan.io/address/0xB483dc6FF0b9554119232edA962862169A13915C#code).&#x20;

### Creating a New Round

New rounds should be created with the Round Factory (see [Contracts](../getting-started/contracts.md)). When a round is created, the following parameters are expected:

* `votingStrategy` - Contract that implements the `IVotingStrategy` interface and casts a vote in a round.
* `payoutStrategy` - Contract that implements the `IPayoutStrategy` interface and distributes the matching pool at the end of the round.
* `applicationStartTIme` - a Unix timestamp for when a Round can start to accept applications.
* `applicationEndTime` - a Unix timestamp for when a Round can no longer accept applications.
* `roundStartTime` - a Unix timestamp for the start of a Round (when voting begins).
* `roundEndTime` - a Unix timestamp for when a Round ends (when voting closes).
* `token` - the token used to vote in a Round.
* `roundMetaPtr` - Off-chain metadata for a Round.
* `applicationMetaPtr` - Off-chain metadata for the application form schema (to apply for a round).
* `projectsMetaPtr` - Off-chain metadata for the projects.

### Round Metadata

Here is an example of metadata from the recent Climate Solution round ([original file](https://cloudflare-ipfs.com/ipfs/bafkreighrvkk654o5amjlnim7mc4cf4qvofgx3yeftv2kzjama2phood4e)):

```json
{
    "matchingFunds": {
        "matchingCap": true,
        "matchingFundsAvailable": 333333.56,
        "matchingCapAmount": 10
    },
    "name": "Climate Solutions Round",
    "support": {
        "type": "Website",
        "info": "https://support.gitcoin.co/gitcoin-knowledge-base/"
    },
    "eligibility": {
        "description": "The first Gitcoin Program Alpha QF rounds on the Grants Protocol will run from January 17th to January 31st and are only open to a subset of existing grantees. There will be 3 separate matching pools: Web3 Open Source Software, Ethereum Infrastructure, and Climate Solutions, each with its own grant explorer page where you can donate to grants in that round.   The Grant must be primarily focused on climate solutions (the group may do other work but the grant proposal should be directly related to climate solutions).  The proposal should explicitly outline how this project will help reduce GHGs or is important core infrastructure for web3 climate solutions. ",
        "requirements": [
            {
                "requirement": "Grantees who received funding in previous rounds must report on updates to their project and how funds are being allocated. This will ensure accountability to supporters and also help encourage contributors by showing what you’ve been accomplishing. "
            }, 
            {
                "requirement": "Grantees can be eliminated from consideration in the round if they are found to be encouraging or enabling sybil attacks or other forms of malicious manipulation of the grants platform or the Gitcoin community. "
            }
        ]
    },
    "programContractAddress": "0xe46e67fcfc40610b60aa7c8ad53263441e138097"
}
```

### Application Metadata

Here is an example of metadata from the recent Climate Solution round ([original file](https://cloudflare-ipfs.com/ipfs/bafkreihtv4v57hm7vpwyothfk44h4i7c2h4sg4zjmmkk4ii4leyg6wxvui)):

```json
{
    "lastUpdatedOn": 1673028127935,
    "applicationSchema": [
        {
            "id": 0,
            "question": "Email Address",
            "type": "text",
            "required": true,
            "info": "",
            "choices": [],
            "encrypted": true
        },
        {
            "id": 1,
            "question": "Funding Sources",
            "type": "text",
            "required": true,
            "info": "",
            "choices": [],
            "encrypted": false
        },
        {
            "id": 2,
            "question": "Team Size",
            "type": "text",
            "required": true,
            "info": "",
            "choices": [],
            "encrypted": false
        }
    ]
}
```

### Applying to a Round

Once a project has been listed in the [Project Registry](project-registry.md), then it can apply for any round on the Allo Protocol. Every Round contract will have a `applyToRound(bytes32,MetaPtr)` method, which accepts a project ID (provided by the Project Registry) and the project meta data.

### Voting in a Round

To vote for a project in a Round, users will call the `vote(bytes[])` method on the Round implementation, which in turn will call the same method on the [Voting Strategy](voting-strategy/).
