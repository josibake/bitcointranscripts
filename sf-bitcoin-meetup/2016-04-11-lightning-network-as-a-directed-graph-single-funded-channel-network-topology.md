---
title: Lightning Network As A Directed Graph Single Funded Channel Network Topology 
transcript_by: Bryan Bishop
categories: ['meetup']
tags: ['multisig', 'lightning', 'segwit']
speakers: ['Tadge Dryja']
date: 2016-04-11
media: https://www.youtube.com/watch?v=-lgYYz3y_hY
---

<http://lightning.network/>

slides: <http://diyhpl.us/~bryan/papers2/bitcoin/Lightning%20Network%20as%20Directed%20Graph%20Single-Funded%20Channel%20Topology%20-%20Tadge%20Dryja%20-%202016-04-11.pdf>

Okay. Hello everyone. Yes, okay, great. Yes. Yeah, I'm working on Lightning Network. Bitcoin is great and fun to work on.

# Basic super quick summary of Lightning

I am going to re-cap lightning network real quick. The basic idea is to have channels between two nodes. A channel, from the perspective of the rest of the world, is just a 2-of-2 multisig output. You can't even tell it's multisig, you just see an address with a 3 in front of the address. Or in the case of segwit, we don't know what the addresses will look like yet, but a 32-byte address.

You make a channel with a counterparty. You keep updating the channel state with your counterparty. You can close cooperatively by interactively signing. You can close non-cooperatively by having these stored signatures from the previous updates. And then you can have multi-hops with HTLCs, timelock contracts. You can think of this as a connected graph of HTLCs. You can have these off-chain payments. In non-adversarial conditions, when people get along and are not jerks, you have big speed improvements. When people are being jerks (adversarial conditions), you fall under similar underlying security as the bitcoin network. You are not trusting them.

If you are getting along with your counterparty, you can go along much faster. And if this person is trying to take your money, they still can't. So that's great.

# When to make a new channel

But how do you build this thing? Assuming the software works, how does this get built? Suppose that A and B want to make a channel. But do they want to? Do they really need to make a channel? You want this to work in an anonymous setting. Dual-funded channels are useful but they are trippy because you need this confluence of wants, where A and B both need to fund the channel, they create a multisig address together, etc. There's some cruft involved in that. You're signing your UTXOs with this person that you might not know. Do you really want to do that? Do they really want to make a channel with you? Maybe they just want to waste your time, or waste your money's time. You don't lose much, but you lose the opportunity that maybe you were making another channel. So there are timing issues, identity issues, etc.

# Dual-funded channels

Some people say that there will be "hubs", but those are actually really tricky.... "hub" is not in the paper by the way, I've never said "hub". I assume that when people say "hub" they mean a node with a high degree. You're going to run a node, accept incoming channel creation, and you're not going to use money much, you're just going to be in the middle oruting... well that's tricky. If someone wants to make a channel with you, do you match their collateral? If you let them fund the channel themselves, then you're not able to route money because the leaves have all the money. So they can't actually route through you in that case. But if you put up collateral, say you're matching or something, 1-to-1 BTC matching... well, that opens you up to denial-of-service attacks where anyone with a lot of BTC can do that a thousand times or something and tie up most of your capital, even if they aren't going to use those channels. Dual-funded channels are useful and powerful, but it's hard for those reasons.

# Single-funded channels

What about single-funded channels? A wants to make a channel with B. A is the one who is initiating. They say "Hey B, give me a pubkey" and B will be like, sure, a pubkey is costless, the worst you can do is disappear or send money to my pubkey, so sure I will generate this and give this to you... So when the channel gets opened, B is able to sign off on refunds and stuff. A gets to decide on the initial state. Maybe there's an initial payment, by making a channel and sending you a bitcoin in that channel. B will say okay but I won't accept this as a payment until this channel is confirmed, but that doesn't take any longer than a normal payment, so sure.

# Channel exhaustion

There's one thing to be aware of. This channel is "off-chain". The money is all one side of a channel. This is dangerous. Say Alice and Bob have a channel. Say Bob has all of the money in the channel. Alice says OK, this is state 5 of the channel. But back at state 4, she used to have BTC, but at state 5 she has no BTC. Alice has nothing to lose by broadcasting an invalid previous state, so it's dangerous for the other person. So basically, don't accept a state transition that exhausts the other counterparty in the channel. You always need this buffer in a channel. It becomes cheap or free for the counterparty to close the channel the wrong way, which is an attack, once exhausted.

There is an exception to this, when there's no previous state, with no funding state, then there's no exhaustion attack that can happen. Let's say A-B channel creation scenario again. A says I want to make a payment in a channel, state 0 of the channel is that A has a coin and B has nothing. B knows that the worst that can A can do is broadcast state 0 to the blockchain and close the channel. The whole thing is pointless to B, but whatever.. B doesn't lose anything.... This is the channel exhaustion exemption. Pushing money to the other counterparty as part of the channel creation process. There can also be a zero funding channel opening event, where A keeps the bitcoin, where B doesn't lose anything, and the good thing is that B has a bigger degree which is kind of nice.

However, after the initial state, you do need to make sure about exhaustion scenarios not occurring. But during that initial state that's fine to have that imbalance.

# Simple UI

The simplest UI is something like, A, the actual flow will be that A this destination this amount. First you look at whiich channels you have available, do I have money to route using the existing channels. If you can do that, great, done, use that. If you can't, open a channel for the full amount, push the full amount to the counterparty, and then you're done. That takes 10 minutes, whereas using an existing channel would only take a few seconds. There's no extra cognitive load, this looks exactly like the UI for making current bitcoin transactions.

This can also scale. If channels start as exhausted, it's a directed graph, but over time it could become bi-directional. Anyone trying to use these funds could use these channels. The arrows might point the opposite direction, even if you are using that simple UI scenario. Alice sends a coin to Bob through a channel, there's an arrow from Bob to Alice in the sense that Bob can send payments to Alice in the future but they cannot go in the other way. If someone has a cycle, you could equalize the channels that way in the graph.

<https://www.youtube.com/watch?v=-lgYYz3y_hY&t=10m25s>




