![Zeromev-Geth](/zeromevgethlogo.png)

# tl;dr

Zeromev-Geth is a fully decentralized solution for Ethereum validators wishing to: 
- protect users from [frontrunning](https://info.zeromev.org/terms#frontrunning) and [censorship](https://info.zeromev.org/terms#censorship)
- protect the network from [builder centralization](https://ethresear.ch/t/two-slot-proposer-builder-separation/10980/10) and network censorship under [MEV-Boost](https://ethresear.ch/t/mev-boost-merge-ready-flashbots-architecture)
- protect Ethereum from reduced adoption due to [toxic MEV](https://info.zeromev.org/terms.html#toxic-mev)
- protect themselves from the moral and [legal jeopardy](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4187752) of frontrunning Ethereum users (not legal advice- please consult a lawyer!)
- preserve the opportunity to extract neutral MEV themselves by backrunning
- generally [do the right thing](https://youtu.be/739XYgoA-x8?t=31) :)

Zeromev-Geth is an experimental fork of [Geth](https://github.com/ethereum/go-ethereum) for use as a validator execution client. It will result in reduced staking rewards due to the reduction in [toxic MEV](https://info.zeromev.org/terms.html#toxic-mev). Use at your own risk.

Please read our [faq](#faq) below and our [proposal document](https://info.zeromev.org/zmgeth-proposal.html) for more details, and visit us at [zeromev](http://info.zeromev.org).

# faq

### how does zeromev-geth defeat frontrunning?

It does so by ordering transactions by time instead of by gas price or MEV.

Using unmodified Geth or MEV-Boost, users are frequently frontrun by seconds, minutes or hours. 

The most Zeromev-Geth permits an attacker to frontrun their victim by is about 500 milliseconds (half a second). These remaining [frontruns may be eradicated](https://info.zeromev.org/zmgeth-proposal.html#frontrunning-within-network-latency) for wallet users by connecting directly to wallet providers and distribution networks.

This prevents an attacker from frontrunning a transaction seen in the p2p network by getting their transaction to the [proposer first](https://medium.com/initc3org/strategic-latency-reduction-in-blockchain-peer-to-peer-networks-6599bf38fd53) via a more [direct route](https://bloxroute.com/).

In any case *validators using this fork will no longer be accepting bribes for frontrunning* (either via MEV Auctions or Gas Price Auctions).

### does this require specialized hardware or network configuration?

No. There are two proposed methods of frontrunning protection.

One requires adding a few specific [static nodes](https://geth.ethereum.org/docs/interface/peer-to-peer) as they become available. The other requires signing up to distribution networks like Infura and entering connection details into the Zeromev-Geth command line.

That's it!

### does this expose my validator to risk?

Wallets and distribution networks may choose to expose private connection details to you, although this is no longer a requirement. You do not need to expose your validator details to them any more than you would [normally](https://ethereum.org/en/developers/docs/networking-layer/#consensus-discovery). 

So the security of your validator is not compromised.

### how does this impact decentralization?

It offers roughly the same level of decentralization. You are just optimizing your connections to wallet providers to protect their users from frontrunning.

Existing connections to other nodes through the [discovery network](https://ethereum.org/en/developers/docs/networking-layer/#consensus-discovery) are not impacted and you will be improving mempool performance by reducing the latency of the network overall.

### what happens if the wallet nodes go down?

Even if all of these direct connections are unavailable, frontrunning is still limited to around 500ms under zeromev-geth in the worst case, a vast improvement over the status quo.

### how does it protect against centralization?

MEV auction technologies such as [MEV-Boost](https://ethresear.ch/t/mev-boost-merge-ready-flashbots-architecture) centralize block content around a few dominant builders and encourage the mempool to be bypassed by those extracting MEV and the users seeking protection from them.

Zeromev-Geth restores your power to self-build in a way that protects the network from this centralization.

### how does it protect against network censorship?

By using MEV-Boost you inherit the censorship preferences of the dominant builders. Because they are easy targets for external pressure, this makes the network itself easier to censor.

Self-building reclaims the power you have as a validator to make these decisions for yourself instead.

### how does this impact validator rewards?

Staking rewards will be reduced in line with the reduction in [toxic MEV](https://info.zeromev.org/terms.html#toxic-mev).

How much by depends on the option your select:

available now|option|description|
|---|---|---|
|✔|altrusim|transactions are included in time order, regardless of the tip. This is the lowest reward option|
|❌|base fee percentage|transactions are included in time order as long as they tip at least a percentage of the base fee (similar to a fee payment to a centralized exchange). Validators always get a reasonable tip for inclusion but cannot be bribed for priority|
|❌|selection by gas|transactions are selected by gas price as normal, but then ordered by time. Validator rewards are in line with unmodified Geth, while not permitting frontrunning within their blocks|

(Please note: only the first option is available in this early version - the others are currently under development.)

Also, you still have the opportunity to extract [neutral MEV](https://info.zeromev.org/terms.html#neutral-mev) by [backrunning](#can-I-still-extract-non-toxic-MEV) for futher rewards.

If you are a Go/Geth dev and want to help with furthering these important features, please contact us :) (pmcgoohan@zeromev.org)

### can I still extract non-toxic MEV?

Yes. You are free to backrun any transaction as you receive it without breaking the time ordering regime of Zeromev-Geth. 

This is because as the validator building the block, you have a zero latency advantage over anyone else doing this.

As Zeromev-Geth develops, we hope to build backrunning functionality into the codebase.

We also hope for collaboration with third party backrunning helper services that can offer such services for a fee, along with low latency cross-chain feeds.

### is spam likely to be a problem?

Where frontrunning is defeated, there will be no frontrunning transactions, so the blockspace usually taken up by them will be reclaimed.

Excessive transaction volume from backrunning is reduced by [validators backrunning](#can-I-still-extract-non-toxic-MEV) themselves. There are reasons to think that even without this the problem will not be severe under time ordering as compared to Gas Price Auctions.

For a discussion on this topic, please see our [proposal document](https://info.zeromev.org/zmgeth-proposal.html#backrunning-latency-battles).

### is using a fork of Geth risky?

There are always risks involved in using a forked version of any software. We provide no guarantees of fitness for purpose of this alpha release, and you use the software at your own risk. 

That said, only 3 lines of code have been changed from the original Geth in this first version.

By contrast under Proof-of-Work Ethereum, 90% of miners used a [fork](https://github.com/flashbots/mev-geth) of Geth with thousands of lines of code changes and additions.

### will this result in DDOS attacks?

There is no DDOS vector because of EIP-1559. The base fee increases with demand for blockspace making an attack of this kind prohibitively expensive.

# building

For detailed instructions on Geth please read the [instructions](https://geth.ethereum.org/docs/install-and-build/installing-geth) and [readme](https://github.com/ethereum/go-ethereum/blob/master/README.md).

Building `geth` requires [Go](https://go.dev/). Once installed, run

```shell
make geth
```

# usage

- Install Zeromev-Geth in place of your validator execution client
- Zeromev-Geth [reduces frontrunning](https://info.zeromev.org/zmgeth-proposal.html#frontrunning-within-network-latency) most successfully when configured with static nodes used by the most popular user wallets
- These have not yet been published by wallet node operators. Please check back here for updates
- Please note that this early version only allows only for [altruistic ordering](https://info.zeromev.org/zmgeth-proposal.html#altruism) meaning reduced rewards for validators
- Zeromev-Geth is **not compatible** with MEV-Boost

# license

The go-ethereum library (i.e. all code outside of the `cmd` directory) is licensed under the
[GNU Lesser General Public License v3.0](https://www.gnu.org/licenses/lgpl-3.0.en.html)

The go-ethereum binaries (i.e. all code inside of the `cmd` directory) is licensed under the
[GNU General Public License v3.0](https://www.gnu.org/licenses/gpl-3.0.en.html)
