---
layout: post
title:  "Blind Digitial Signature Voting Scheme"
date:   2017-04-12 19:19:21 +0700
tech: true
---

**Original post:** [link](https://steemit.com/cryptography/@robertdurst10/blind-digital-signature-voting-scheme-implementation)

## Basic implementation of a Blind Digital Signature Voting Scheme in Python using RSA

![dancing cat](https://steemitimages.com/p/5CEvyaWxjaEsVUnNGyqbkS7pRBtq76DUohca25kDEK9zduSRqoj5q8gxdDtHmYQgk4gQCQQvtX5vxqZbb?format=match&mode=fit)

You may be wonder, "What the hell is a blind digital signature voting scheme?"
Well, let's break it down into pieces. This is essentially RSA + digital signatures + blind signatures + voting. We will look at this step by step!

### RSA

RSA is a public key cryptosystem. Essentially, Alice comunicates with Bob by taking Bob's public key and encrypting a message with that key. Then Alice sends the encrypted message to Bob. Bob receives the ciphertext (encrypted message) and uses his private key to decrypt the message. Then Bob can read the message. This is secure because only Bob can read the message from Alice because only he has the private key to decrypt the message. EX) Think of your mailbox. Anyone can put mail in your mailbox, but only you have the key to open it. From a technical standpoint, this works as so:

    Key Generation:
        Pick two large primes. Lets call those two primes p and q.
        Calculate:
            a) n = (p)(q)
            b) phi(n) = (q-1)(p-1)
            c) e = (relative prime of the units of phi(n)) * Higher Level Math Term *
            d) d = modular inverse of e * Higher Level Math Term *
        public key = (n,e) and private key = (n,d)
    Encryption
        Receive Bob's public key (n,e)
        Choose m, message
        Encrypt message --> m^e = C
        Send C to Bob
    Decryption
        Receive message C
        Remember private key (n,d)
        Decrypt message --> C^d = m

### Digital Signatures

Digital signatures work sort of like real world signatures. Whenever you sign a receipt, you are verifying that you agree with the purchase and you are using your credit card; essentially you are "confirming" your identity. Confirming is in quotes because this system is archaic and not reliable. Digital signatures are essentially a mathematical signature that cannot be replicated (at least not easily).

As with RSA, digital signatures use public and private keys. Say I want to send a bitcoin to Bob, well, Bob would want some way of verifying that I in fact agreed to that transaction (everyone likes free money, but Bob doesn't want me claiming later I was cheated). Well, I can digitally sign the transaction. Essentially, in the Bitcoin world, people are identified by pseudo-anonymous addresses that are random characters. For each address, the owner of the wallet owns a private key. Through math, the only one who may create messages or send transactions from a wallet is the one who possesses the private key associated with that address.

Blind Signatures and Voting Scheme (with RSA)

Consider now a system where someone needs something verified but doesn't want the signing party to know the data they are signing off on; the best example is at a voting center. While a voter needs an election official to confirm their voting eligibility and authorize their vote, they don't necessarily want the official to know who they voted for. Thus we have blind signatures. Here is a technical implementation of a blind signature scheme (using RSA), as outlined by David Chaum in this paper:

    Signing Authority Creates Public and Private Information:
        a) Generates random p and q
        b) Computes n=pq and phi(n)=(p-1)(n-1)
        c) Picks e such that e is a relative prime in the field of units modulo phi(n) * Higher Level Math Term *
        d) Computes d, where d is the inverse of e modulo phi(n) * Higher Level Math Term *
        e) Publishes to PUBLIC: (n,e)
    Voter Prepares Ballot for Signing by Signing Authority:
        a) Generates random r such that 1<r<p
        b) Chooses favorite candidate, option, etc. on ballot
        c) Creates message: m = candidate + r
        d) Generates f such that f is a relative prime in the field of units modulo n * Higher Level Math Term *
        e) Computes g, where g is the inverse of f modulo phi(n) * Higher Level Math Term *
        f) Computes blinded message (disguises his message): m' = m*f^e mod n (where n and e are public knowledge)
        g) Sends m' to signing authority
    Signing Authority Authorizes Ballot
        a) Signing authority receives m'
        b) Signing authority verifies voter is eligible to vote
        c) If voter is eligible, signing authority signs ballot: s' = (m')^d (where d is the secret exponent of the signing authority)
        d) Sends s' back to voter
    Voter Unwraps Blinding of Ballot
        a) Receives s'
        b) Computes s = (s')(g)
        c) Sends the signature s in to the ballot receiving location
    Ballot Received, Verified, and Counted
        a) Compute:
            1) ballot = s^e
            2) signature = s
            3) verification (true or false) --> Use ballot, signature, (public e, aka public key of the signing authority)
        4) vote = ballot - random-extra (predetermined length of random-extra makes easy to subtract)

### Conclusion

So I really hope all that made sense! If you have any questions please ask below! If there are any small mistakes, SORRY, it has been a long day.

If you want to try this out for yourself, I uploaded the code to my GitHub: https://github.com/robertDurst/BlindVoting

To run the code:

    1. Download
    2. Download necessary modules (I think you just need websocket)
    3. Open two terminals
    4. Terminal 1: python web_app.py --> The result window of the polling
    5. Terminal 2: python electionPoll.py --> The polling window

Again, comments, questions, love, accepted below!

Cheers!