---
title: An experiment with Whisper
author: Mark Spanbroek & Stefan van den Oord
layout: post
---

We’ve been looking at Whisper, a messaging system that runs on top of the ÐΞVp2p peer-to-peer network. The same network that is run by all Ethereum nodes.

It has a number of characteristics that, in time, could make it the standard component that developers reach for when they need to add messaging to their code.

Being based on a peer-to-peer network, you do not need any servers. When you’re developing an app, that is a big win. No contracts with cloud providers, no server infrastructure to maintain.

Whispers use of encryption and a smart routing mechanism ensure that both the contents of messages and the metadata (who talks to whom) are kept private. This is not only a big win for the users, but also for the developers. Not having access to the data of their users means less liability. Developers cannot accidentally leak the data, they cannot be coerced to hand it over to a third party and they cannot censor it.

### Our prototype

We would like to see how far we could get in using Whisper in a normal development setting. So we decided to start a prototype of a chat app using Whisper.

We ended up with the following architecture:

    --------------------------------
    |  Our Simple Chat App         |
    |                              |           -----------------
    |   -----------------------    |           | Ethereum Node |
    |   | The Shhwift Library |    | JSON-RPC  |               |
    |   |                     | <----------------->            |   
    |   -----------------------    |           |               |
    --------------------------------           -----------------

We start an Ethereum node (in this case [Geth][1]) and connect to it using its [JSON-RPC interface][2]. For most of our testing we ran the ethereum node on a computer, and connected to it from the phone.  We also attempted to run an Ethereum node on the phone with limited success.

We created a library we called [Shhwift][3] that wraps the JSON-RPC methods for Whisper in Swift, so that we could easily call them from our app.

And we created a very simple chat application that uses the library to send and receive messages.

To avoid having to deal with key-exchange, we only sent broadcast messages that can be received by anyone listening for them.

### Outcome

With this setup we were able to send messages from one phone to the other over the internet. Every now and then. Maybe.

Only when all the conditions were right then the messages would arrive. (both nodes were lucky enough to find and hold on to a Whisper enabled peer, the messages propagated through the peer-to-peer network in time before they expired). Most of the time they wouldn’t.

### Shortcomings

We found several areas in which we think that Whisper falls short of being a great solution for real world applications.

The documentation of Whisper is sparse, and not always correct. After implementing the Shhwift library according to the [JSON-RPC spec][2], we had to change the implementation [in][10] [several][11] [ways][12] in order to make it work with Geth.

It is not easy to run any implementation of Ethereum [on a phone][4]. We were able to [build a version of Geth][5] for iOS, but it is severely limited. It only works on 32-bit iOS devices. It doesn’t work on the iOS simulator. And it will start downloading the complete Ethereum blockchain, which is several gigabytes in size.

The license of the two main Ethereum implementations is (L)GPL, making them unsuitable for use in iOS apps, because it [prohibits publication on the Apple App Store][6].

You have to explicitly enable Whisper when starting an Ethereum node. This means that almost no-one is running a Whisper node, which makes it hard to propagate messages between peers. The fact that there are two incompatible implementations of Whisper only exacerbates this issue. Geth implements version 2 of Whisper, whereas Ethereum-C++ implements version 3.

Because there is no large-scale Whisper network running yet, the performance of it running at scale is currently unknown.

It seems that most of the development activity in Ethereum is focussed on the blockchain, and less on Whisper. Little progress has been made on answering the fundamental questions such as scalability.

### The good news

Fortunately almost all of these shortcomings are being addressed.

Significant work is being done to make Ethereum suitable for mobile phones. Zsolt Felföldi is implementing a light client in Geth that entered a [public testing phase][7]. The Ethereum-C++ client is likely to follow suit.

The [licensing page][8] of Ethereum mentions the intention to eventually arrive at a license that allows “use in any commercial environment, closed or open source”.

Whisper will likely be enabled by default in a future version of Ethereum, creating a large network of Whisper nodes.

### Conclusion

Our attempt to create a chat prototype based on Whisper was driven by a desire to learn more about its possibilities and shortcomings.

We have seen the potential that Whisper can grow into a widely used solution for messaging with major improvements over the currently available solutions.

The fact that there are shortcomings is only natural considering the maturity of Whisper. It is clearly marked as being an experimental prototype that needs further work. It seems that most of these shortcomings are being addressed.

The big remaining question is whether Whisper will perform at scale. Only time will tell.



[1]: https://github.com/ethereum/go-ethereum
[2]: https://github.com/ethereum/wiki/wiki/JSON-RPC
[3]: https://github.com/charterhouse/Shhwift
[4]: http://markspanbroek.github.io/2016/06/02/ethereum-light.html
[5]: https://github.com/markspanbroek/Ethereum-iOS
[6]: http://www.fsf.org/news/2010-05-app-store-compliance
[7]: https://github.com/zsfelfoldi/go-ethereum/wiki/Light-Client-Public-Test
[8]: https://github.com/ethereum/wiki/wiki/Licensing

[10]: https://github.com/charterhouse/Shhwift/commit/a808645ec0ae2974df64d5a6c47a14cb1e51660a
[11]: https://github.com/charterhouse/Shhwift/commit/cf1f61183de07c362852adc5db18cbb464b2c2de
[12]: https://github.com/charterhouse/Shhwift/commit/bef92ebf802af3e7212ae272c9967c98544c6f9e
