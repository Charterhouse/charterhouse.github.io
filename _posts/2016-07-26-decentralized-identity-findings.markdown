---
layout: "post"
title: "Decentralized Identity - Findings"
author: Stefan van den Oord, Mark Spanbroek & Mark Hennessy
---

This week we have done research and experiments around the topic of Decentralized Identity. We started by writing down [our thoughts on what decentralized identity is about][1].

There is a lot to take in on this topic. If you're not careful to limit the scope, it quickly becomes large and complicated. For example, it may seem logical to make an identity system hierarchical in terms of how strong an identity is. Higher up in the hierarchy identities are stronger (think e.g. identities issued by governments), but for things like simple web site logins you want to use an identity lower in the hierarchy.

### Identity management

This made us think of BIP32 and HD wallets. We learned that there are some intricacies when dealing with hierarchical keys. For instance under certain conditions it is actually [possible to prove][5] that two public keys have a parent-child relationship. Or if you’re not careful, you could inadvertently [expose your private key as well][18]. So you need to be careful about exposing public keys, which given their name is rather unexpected. We also learned what the [difference is between a hardened and a non-hardened public key][20]: hardened keys are not vulnerable to the above, but if you want to derive public keys from another (extended) public key, those derived keys are non-hardened.

As an aside: we were wondering what would be a good name for an application that manages your identities. The term “wallet” suggests “money”, not “identity”.

### Attestation

Another part of identity is _attestations_. You can setup an identity system so that other participants of the identity system can attest that you are really you. Or, using [Christopher Poole's analogy][6] of facets of a diamond: that a given facet of your diamond is valid. For example, a university can attest that you have truly graduated there. A government can attest that you are a citizen of that country. A sports club can attest that you are a member of that club. And then you get into things like: how long is an attestation valid? How do you revoke an attestation? How do you calculate the strength of in identity based on the attestations? There is also [work][7] done on a logical framework for dealing with attestations so that you can prove certain claims, for example.

### Selective disclosure

When you talk about using identity in practical applications, the term "selective disclosure" will pop up sooner or later. It simply means the ability to show or hide information to specific identities. This is complicated to get right, and also application dependent. For example in a medical context, when a patient logs his weight in a medical record like system, she can determine who can see that information. But suppose she is treated for something by more than one doctor. Doctor 1 stores her blood pressure, and only the patient and doctor 1 can see it. Then doctor 2 comes along. He cannot see that the blood pressure is available in the medical record, so how can he request access? What happens if he also stores a blood pressure measurement: can doctor 1 see it or not? Scenarios like that need to be carefully considered in the proper context.

### Blockstack

There are a number of decentalized identity systems in development. We'll quickly touch upon a few of them.

[Blockstack][8] is an identity platform based on blockchain technology. It does not require a specific blockchain, but because it is currently the most reliable one it is overlaid on top of the Bitcoin blockchain. In the Blockstack [white paper][24], the authors outline a number of other reasons that they moved from [namecoin][23] to bitcoin that relate to superior security of the bitcoin blockchain (due to it's size and overall hash rate as well as the networks stability compared to namecoin). It has a [pricing scheme for registering identities][9], and it supports [attestation][10].

Blockstack has a layered architecture. It maintains a naming system as a separate logical layer on top of the underlying blockchain on which it operates. Blockstack uses the underlying blockchain to achieve consensus on the state of this naming system and binds names to data records. Specifically, it uses the underlying blockchain as a communication channel for announcing state changes, as any changes to the state of name-value pairs can only be announced in new blockchain blocks. Relying on the consensus protocol of the underlying blockchain, Blockstack can provide a total ordering for all operations supported by the naming system, like name registrations, updates and transfers.

Public profiles are constructed through the compilation of public statements, signed by the user that owns the profile. Extended profiles are public profiles extended with other information that has not been made publicly accessible. This information is privately released by the user to selected parties. When a user authorizes another party to access information, the user encrypts the information with the party's key and hosts it in a publicly-accessible location. The authorized party is sent the link to the information and is able to decrypt it and extend the public profile with the private information ([Identity][11], [Profiles][12]).

Clients read data values from the data layer and verify their authenticity by checking that either the data’s hash exists or the data includes a signature with the name owner’s public key.

The user is free to update the private information, such as an address, and the authorized party will be able to continually grab the updated information and keep everything in sync.

	{
	  "address": {
	    "@type": "PostalAddress",
	    "streetAddress": "16 Maiden Ln",
	    "addressLocality": "San Francisco, CA",
	    "postalCode": "94108",
	    "addressCountry": "United States"
	  }
	}

Blockstack supports multiple storage mechanisms. The mechanisms specifically mentioned are AWS S3, IPFS & Syndicate. Data is discovered by hash or URL. Having an off-chain data storage mechanism increases the amount of data that can be associated with a name as it becomes quite expensive to store larger amounts of data in the blockchain. There are also longer term concerns with trying to store private data under PGP schemes like that used by bitcoin in the blockchain directly. This of course could change if blockchains of the future support quantum safe encryption methods.

Attestation is covered in the Blockstack documentation page on [Identity Attestation][10] which allows for verified profiles and software proofs (social networks proofs, domain name proofs, key proofs and facial recognition proofs) where different types of attestation can be done such as peer or authority attestations with all of this added to the immutable profile of the users name record.

As part of our investigation into Blockstack we have used their software to create an online identity. We wrote [a separate post][25] describing our experiences.

### uPort

[Consensys][21] is working on a self-sourced identity system called [uPort][22], using the Ethereum blockchain and IPFS for storing your personal data. It purportedly includes a selective disclosure mechanism and a reputation system. They are collaborating with Canonical, Microsoft, BlockApps and Blockstack but haven’t released any source code yet.

### ChainAnchor

MIT seems to be developing an identity system that attempts to address [not only the technical and privacy aspects of identity, but also the legal framework][2]. It’s called [ChainAnchor][3], but there is not much information available yet.

### Eris

As always, we wanted to try and actually write some code as part of this investigation. We wanted to do that with smart contracts. So we started looking into [Eris][13], a "smart contract application platform". Using Eris, it is easy to setup your own private blockchain and start experimenting. It is free that way, you don't need any Ether or something similar.

Since this is the first time that we wanted to write our own smart contracts, we started looking at their documentation. We were impressed. They have good [tutorials][14], both on how to setup the system, and on Solidity. We particularly enjoyed reading their articles about architecture of Solidity contracts ([The Five Types Model][15] and [An Action-Driven Architecture][16]).

Unfortunately time ran out before we could actually build our own first simple identity system. But we do have some ideas on how to continue from here.

### Further reading

If you’d like to read more about decentralized identity then this collection of [Blockchain Identity links][4] is a good starting point, as are the documents from the [Rebooting the Web-of-Trust][17] workshops.

[Questions? Comments?][19]

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
[18]: https://bitcoinmagazine.com/articles/deterministic-wallets-advantages-flaw-1385450276
[19]: https://github.com/Charterhouse/charterhouse.github.io/issues/3
[20]: http://bitcoin.stackexchange.com/questions/37488/eli5-whats-the-difference-between-a-child-key-and-a-hardened-child-key-in-bip3
[21]: https://consensys.net
[22]: https://medium.com/@ConsenSys/uport-the-wallet-is-the-new-browser-b133a83fe73
[23]: https://namecoin.info/
[24]: https://blockstack.org/blockstack.pdf
[25]: blockstack.html
