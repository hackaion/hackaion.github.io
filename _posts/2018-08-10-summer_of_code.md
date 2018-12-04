---
layout: post
title: GSoC 2018 - Final Report
description: My last report for this adventure :)
keywords: "gsoc, mitmproxy"
---

## A Summer of Code

Here I write my final report of Google Summer of Code 2018 for mitmproxy, under
the Honeynet organization. I'll include pretty much all the work done for
the organization, together with the ideas behind every step.
Use it to get the code, or read about my work and contact me while
I extend it, to be part of this awesome team :)

Shortly, my project for these months was to develop a new serialization module
for mitmproxy _Flows_. This is useful in a number of ways, most of them underlined
in my GSoC [project overview](https://summerofcode.withgoogle.com/projects/#5438256557064192).
One peculiar idea was to exploit this module to implement a Hybrid SQLite DB which
could serve as the storage layer for mitmproxy -- not only solving RAM chugging issues,
but serving as the foundation to implement persistence in the software, aka
**Sessions**.

### Testing

Before spending time and resources to properly implement a serialization interface
under Google Protocol Buffers, one crucial point was to test the libraries (protobuf and sqlite), gathering
some fact to ensure that **serializing wouldn't have too much impact on performances**.
In the meantime, it proved to be a good time to learn more about _asyncio_ code
in Python, which anyway is the approach adopted by mitmproxy since
[release 4](https://mitmproxy.org/posts/releases/mitmproxy4/).

#### Pull Requests

- [Test Dumping Performances](https://github.com/mitmproxy/mitmproxy/pull/3126)

This has been my first PR for GSoC. It included a lot of testing code: a dummy
serialization interface, and two addons which measured **dumping time in comparison
with tnetstrings**(_dumpwatcher_), and **throughput to SQLite DB when flows are streaming costantly**(_streamtester_).
I, later in time, adjusted that code - using the new, complete protobuf interface - to
be used as a benchmarking script in test folder:

- [Protobuf Benchmarking Script](https://github.com/mitmproxy/mitmproxy/pull/3256)

This can be used as it is just by running the script, as with every other
mitmproxy script. It can be modified by tweaking params, or saving path, or
flow dimensions and fields.

### Refactoring the View API

Building a new storage layer means building a new View. This addon, in fact, handles
the logic to implement the ordered, filtered storage every tool (web or console)
offers in its interface. The View offers an API exposed via commands to the tools,
but some of the code still accessed the addon internally. So, I started cleaning
up the API, extending it, and clearing most of the "illegal" accesses to the addon.

#### Pull Requests

- [View Cleanup](https://github.com/mitmproxy/mitmproxy/pull/3202)

The cleanup is not completed yet. It will be a gradual process, involving changes
in some other area of mitmproxy, to be reworked in conjuction with other devs.
Nevertheless, it was a good start and helped a lot understanding **how** and
**where** mitmproxy stores its flows :)

### Building the Protocol Buffer Interface

Here we go. Serializing flows with protobufs. My idea, during the whole process,
was to mirror the functionalities that _tnetstrings_ and _state_objects_
offered, bringing all the logic into: a .proto file, providing a clear format
for _Flows_, and the protobuf module, where all the conversion code was implemented.

#### Pull Requests

- [Shifting to Protocol Buffer Serialization](https://github.com/mitmproxy/mitmproxy/pull/3245)

The code is merged and can be used by every tool, script or addon interfacing
with mitmproxy by importing  `mitmproxy.io.protobuf`, calling `protobuf.dumps`, `protobuf.loads`
methods. Everything should be clear enough reading the code and its usage in the
the other PRs which use this interface.


### A New Storage Layer for Sessions

Having a functioning, fast enough dumping interface handy, I proceeded with building
the foundations for Session storage. In particular, I have developed an addon (Session)
which implements the logic to use the Hybrid DB (indexed protobuf blobs of flows)
as the storage layer for mitmproxy.

#### Pull Requests

- [Session - Hybrid DB](https://github.com/mitmproxy/mitmproxy/pull/3252)
- [Session - Storage Layer](https://github.com/mitmproxy/mitmproxy/pull/3277)

The two PRs are widely commented, so I'll avoid adding redundancy here.

### The End...?

I'm grateful for the GSoC experience. It encouraged me to explore the Open Source
community, find many amazing developers and persons, while constantly improving
my skills and knowledge. I have found an awesome environment in mitmproxy, and while
the GSoC period ends, my journey with this organization does not. Enjoy!


_Pietro Francesco Tirenna - madt1m_
