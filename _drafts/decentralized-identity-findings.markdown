---
layout: "post"
title: "Decentralized Identity - Findings"
author: Stefan van den Oord & Mark Spanbroek
---

Decentralized Identity - Findings
=================================

This week we have done research and experiments around the topic of Decentralized Identity. We started by writing down [our thoughts on what decentralized identity is about][1].

There is a lot to take in on this topic. If you're not careful to limit the scope, it quickly becomes large and complicated. For example, it seems logical to make an identity system hierarchical in terms of how strong an identity is. Higher up in the hierarchy identities are stronger (think e.g. identities issued by governments), but for things like simple web site logins you want to use an identity lower in the hierarchy. This of course made us think of BIP32 and HD wallets. (Side note: I now understand that an address in Bitcoin is a one-way hash of a public key, and that under certain conditions it is actually [possible to prove][5] that two public keys have a parent-child relationship.) (Other side node: what would be a good name for an application that manages your identities? Like a wallet for bitcoin addresses, but then for identities.)

Another part of identity is _attestations_. You can setup an identity system so that other participants of the identity system can attest that you are really you. Or, using [Christopher Poole's analogy][6] of facets of a diamond: that a given facet of your diamond is valid. For example, a university can attest that you have truly graduated there. A government can attest that you are a citizen of that country. A sports club can attest that you are a member of that club. And then you get into things like: how long is an attestation valid? How do you revoke an attestation? How do you calculate the strength of in identity based on the attestations? There is also [work][7] done on a logical framework for dealing with attestations so that you can prove certain claims, for example.

When you talk about using identity in practical applications, the term "selective disclosure" will pop up sooner or later. It simply means the ability to show or hide information to specific identities. This is complicated to get right, and also application dependent. For example in a medical context, when a patient logs his weight in a medical record like system, she can determine who can see that information. But suppose she is treated for something by more than one doctor. Doctor 1 stores her blood pressure, and only the patient and doctor 1 can see it. Then doctor 2 comes along. He cannot see that the blood pressure is available in the medical record, so how can he request access? What happens if he also stores a blood pressure measurement: can doctor 1 see it or not? Scenarios like that need to be carefully considered in the proper context.

Blockstack
----------
[Blockstack][8] has (is building?) an identity platform based on blockchain technology. It does not require a specific blockchain, but because it is currently the most reliable one it is currently overlaid on top of the Bitcoin blockchain. It has a [pricing scheme for registering identities][9], and it supports [attestation][10].

It was not clear to us how Blockstack treats privacy of profile information. [On their website, they write:][11]

> Profiles can contain both private and public information, which is attested to by the user and can be verified by peers and select authorities.

[And:][12]

> This information is privately released by the user to selected parties.

We didn't find out how this works exactly. Where is that private information stored? How is access to it controlled? This would be something to further look into if we would use Blockstack in applications.

### MARK HENNESSY ABOUT SETTING UP BLOCKSTACK SERVER

uPort
-----
### TODO

Eris
----
As always, we wanted to try and actually write some code as part of this investigation. We wanted to do that with smart contracts. So we started looking into [Eris][13], a "smart contract application platform". Using Eris, it is easy to setup your own private blockchain and start experimenting. It is free that way, you don't need any Ether or something similar.

Since this is the first time that we wanted to write our own smart contracts, we started looking at their documentation. We were impressed. They have good [tutorials][14], both on how to setup the system, and on Solidity. We particularly enjoyed reading their articles about architecture of Solidity contracts ([The Five Types Model][15] and [An Action-Driven Architecture][16]).

Unfortunately time ran out before we could actually build our own first simple identity system. But we do have some ideas on how to continue from here.

Other Interesting Links
-----------------------
* Hardjono, Pentland, [On Privacy-Preserving Identity within Future Blockchain Systems][2] (About the MIT project [ChainAnchor][3])
* [A collection of Blockchain Identity links][4]
* [Rebooting the Web-of-Trust][17]

[1]: https://charterhouse.github.io/2016/07/18/decentralized-identity.html
[2]: https://www.w3.org/2016/04/blockchain-workshop/interest/hardjono-pentland.html
[3]: http://trust.mit.edu
[4]: https://github.com/peacekeeper/blockchain-identity
[5]: http://bitcoin.stackexchange.com/a/37138
[6]: http://mashable.com/2011/10/18/chris-poole-4chan-web-2/#ou2f5YyAdZqt
[7]: http://doc.utwente.nl/61675/1/thesis_M_Czenko.pdf
[8]: https://blockstack.org
[9]: https://github.com/blockstack/blockstack-server/wiki/Usage#namespaces
[10]: https://blockstack.org/docs/identity-attestation
[11]: https://blockstack.org/docs/blockchain-identity
[12]: https://blockstack.org/docs/blockstack-profiles
[13]: https://erisindustries.com
[14]: https://docs.erisindustries.com/tutorials/
[15]: https://docs.erisindustries.com/tutorials/solidity/solidity-1
[16]: https://docs.erisindustries.com/tutorials/solidity/solidity-2
[17]: https://github.com/WebOfTrustInfo/rebooting-the-web-of-trust
