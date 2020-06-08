---
layout: post
title: Let’s talk about Homomorphic Encryption
date: '2020-05-22 12:16:28'
image: /assets/images/content/cyber-security.jpg
tags:
  - Homomorphic Encryption
  - Encryption
  - Linaro
category: Blog
author: joakim.bech
---
#### **Homomorphic Encryption.**

Encryption is something that all of us are using whether we know it or not. Many of the things that you are doing on a daily basis requires some kind of encryption to protect your information from being read and used by others. Encryption by itself is a big and complex topic, covering topics like source of randomness, different encryption algorithms, different modes, how often to use (or not use) encryption keys. In this blog post we’re going to set aside the traditional encryption as we’re used to and instead have a look at something called Homomorphic Encryption (HE). The word homomorphic in Homomorphic Encryption implies that it is possible to modify data. Malleability usually is a property that we don’t want when working with encryption, which is one of the reasons why we have to add some kind of MAC (HMAC etc.) on top of our encryption to be able to protect the integrity of our ciphertext. Contrary to traditional encryption, this is actually a necessary property when working with Homomorphic Encryption. This will not be a deep dive in the algorithms and the mathematics involved, simply because me as author of this article is still learning about all this. Instead it’s meant to serve as an eye opener to a technology that might be important in a few years from now. In short there are use cases that could serve as a business opportunity and be a source for revenue for the ones willing to invest time and money in this early on.

#### **Technical background.**

Homomorphic Encryption isn’t a new thing, people have been working with this since the late 70’s when researchers were working on RSA (which in its basic form is a multiplicative Homomorphic Encryption scheme, also called Partial Homomorphic Encryption). They posed the question asking what could be achieved with a fully Homomorphic Encryption scheme. Their conclusion was that it should be possible to make computations on arbitrary encrypted data without knowledge or access to the decryption key. Still more than 30 years later we haven’t been able to put it into practical use. But, in the recent decade, researchers have implemented software (see [HElib](https://github.com/shaih/HElib) and [Microsoft SEAL](https://www.microsoft.com/en-us/research/project/microsoft-seal/) for example) and at the same time computational performance in general have seen great improvements and as a result of that researchers believe that we are at the inflection point now, i.e. it looks like it could be used in practice in a not too distant future.

#### **What problems will Homomorphic Encryption solve for us?**

In short, Homomorphic Encryption lets you make computations directly on encrypted data without the need to have access or any knowledge about the key used to decrypt the data. So instead of doing computations on plaintext you can do some computations directly on the ciphertext without first having to decrypt it. The piece of ciphertexts that you make computations on will also result in an encrypted blob and only the one possessing the decryption key will be able to decrypt the message. So it’s obvious that the one doing the computations for us cannot learn anything about the unencrypted data. This sounds interesting, but what are the actual use cases?

Today it’s not uncommon to leverage computing power in big data centers. I.e., you send your data and jobs up to some cloud service, which in turn does the heavy lifting in terms of using lots of computational resources on your data and once complete, you can download and inspect the results. This scenario is fine if you’re using data where you don't have to consider privacy. If you are worried about a man in the middle watching you traffic, then you typically transfer information using an encrypted channel using SSL for example. If you also are worried about having data stored in plaintext at the cloud service provider, then you always pre-encrypt the data before uploading it to the cloud service provider. However, in that case you cannot leverage the cloud service computing power without first decrypting the data on your own (directly at the cloud service providers servers) or by letting the cloud service company have access to your key. In both cases your data will at some point be in the clear which might be a problem or at least a privacy concern. If you have requirements of never exposing data in the clear on remote servers, then at best, the cloud service provider works like an encrypted backup server.

With Homomorphic Encryption, you would still encrypt your data locally, upload it to the cloud service provider, but the big difference now is that the service provider can run jobs and do computations directly on the encrypted data on your behalf. The computed result would still be encrypted and can be downloaded locally and then decrypted. The nice thing here is that it is possible to operate on subsets of the data. Compare this to the “encrypted backup” case, where you basically would have had to download everything locally, do the computations on a subset, encrypt everything again and upload it to the cloud service. If nothing else, a big waste of bandwidth and time spent on doing that.

The computational offloading in itself is one positive thing, but the preservation of privacy is probably the most valuable thing. Since it enables companies, researchers etc. to work with data without knowing about details in the data they are working with. As a very hypothetical example, let’s say that someone wants to find out if there is any correlation between cancer and kidney problems. For such a study to take place today, someone (a person permitted to work with medical records) would first need to scrub the personal information from the data set used in the study before handing it over to the one making the study. That might be sufficient, but with Homomorphic Encryption you could basically write a function that looks up all medical records for cancer patients, compute the correlation between cancer and kidney issues and give us the result. Since it’s Homomorphic Encryption the result would still be encrypted. The server who did the computations for you has no clue what you’ve done and what your result was, but it did all the heavy lifting for you. Promoters of Homomorphic Encryption often mentions secure search queries as one use case. You could for example use something like a Google maps service to search for nearby restaurants and the service would be able to provide a result without knowing what you searched for, who you are and where you are. A third example is electronic voting schemes. Imagine that the voters actual votes only are stored in encrypted form. Then Homomorphic Encryption could add up all voters that have voted for candidate A, candidate B and so on and only present the result without revealing individual votes. As you can imagine there are many use cases and twists like this.

#### **Existing software.**

There are different variants of Homomorphic Encryption called Fully-, Somewhat- and Partially-Homomorphic Encryption. Without going into detail, they differ in the amount of operations they can do and the kind of operations they are able to use in their computations. Out of these three, the Fully Homomorphic Encryption (FHE) is the most capable of them all and that is the one that researchers are trying to realize in terms of both software as well as hardware (FHE accelerators). Why? With FHE you can do both (modular) multiplication as well as (modular) addition. If you think about how our computers are built, they are basically built with (boolean) logical gates, i.e. something that can be constructed with only multiplications and additions. So in theory you can create any type program using FHE.

Earlier in this article we mentioned [HElib](https://github.com/shaih/HElib) and [Microsoft SEAL](https://www.microsoft.com/en-us/research/project/microsoft-seal/). HELib implements FHE using the Brakerski-Gentry-Vaikuntanathan ([BGV](https://eprint.iacr.org/2011/277)) scheme and Microsoft SEAL implement FHE using Brakerski/Fan-Vercauteren ([BFV](https://eprint.iacr.org/2012/144)), both which are about using exact arithmetic on vectors of numbers, i.e. there will be no approximations or errors in the result. There are other schemas out there also, but with different properties and characteristics. Both HElib and SEAL compile and run nicely on a regular desktop computer. I.e. it's not hard to try using this technology on your own. There are other software libraries out there, but these are the ones that we have started to familiarize us with.

#### **Issues with the state of the art implementations.**

Functionality wise the current implementations are working, but the performance is really poor. It’s many orders of magnitude slower to use Homomorphic Encryption compared to working with traditional encryption as we are used to. Therefore, it is still impractical to use in reality. Today's state of the art implementations are working with Linear functions, polynomials and you could even with higher degree polynomials fit nonlinear functions, but with that comes yet additional cost in terms of performance. Another problem is that it’s not easy (even possible?) to implement rudimentary functions. For example doing branching on encrypted data isn’t possible on current implementations, even though that in theory should be doable due to the logic gate reasoning. As a consequence of that, instead researchers are looking for ways to implement specific functions that should support specific use cases. Alongside with that other tricks and optimizations are developed to reduce the number of operations involved. A simple example is that it’s better to do (YxY)2 than (YxYxYxY) if you want to calculate Y4, i.e. three multiplications instead of four multiplications.

Linaro has been working with major SoC vendors for many years and has a great track record of driving joint effort upstreaming work to many well known open source projects, including Linux kernel, UEFI, U-Boot, OP-TEE, TrustedFirmware-A/M and Zephyr to mention a few, that are touching security in one or another way.

Perhaps a collaborative approach could be the way forward for the future?