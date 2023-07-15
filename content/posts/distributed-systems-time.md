+++
author = "Harsh"
title = "Distributed Systems: Time"
date = "2023-01-10"
description = "How time works in a distributed system"
tags = [
    "distributed systems",
    "system design",
    "microservices",
]
+++

Time is arguably one of the most critical concepts in any application, especially in distributed ones: Setting timeouts, measuring performance, caching, logging, and most other operations require some measurement of time to operate. <br>
Time works intuitively in a single-threaded application, where one operation happens after the other. However, in distributed systems, where processes might run concurrently and without a physically synchronous global clock, it becomes a real challenge to measure time and track events' order. <br><br>
**In this post, we will explore Physical and Logical clocks, the algorithms for implementing them, and their role in distributed systems**. <br>

### Physical Clock

Physical clocks measure time in seconds, and all processes have access to physical clocks based on **vibrating quartz crystals**. <br>
Quartz clocks are cheap, though they are not entirely accurate due to manufacturing defects and the external temperature, and these factors can make such clocks run faster or slower than others.

> The rate at which a clock runs (fast or slow) is called **drift**, and the difference between two clocks at a specific time is called **skew**.

Another more accurate physical clock is an **atomic clock** which measures time based on the resonance frequencies of atoms. <br>
Atomic clocks are highly precise however they are too expensive and bulky to replace Quartz clocks so, to solve the issue of drifting, Quartz clocks are synchronized with atomic clocks from time to time using the **Network Time Protocol (NTP)**. <br>
At the core, NTP utilizes a clock discipline algorithm to adjust an NTP client's clock time based on the time received from an NTP server.

> Every mainstream operating system comes with an NTP client built-in.

Time synchronization using NTP might seem trivial on the surface but in practice, this is far from the truth - network latency and node inaccuracies can make this a challenging feat. Thus a combination of selection and filtering algorithms are used to achieve accurate time synchronization. <br>

Using the NTP, a client can adjust its time to the most accurate time, however, this solution introduces a substantial flaw - the adjustment of time by the client can cause the clock to jump forward or backward in time which makes the measurement of time error-prone, for example, we might record inaccurate execution time of an operation due to drift. <br>

Physical clocks drift, as such, most operating systems offer **monotonic clocks**. These clocks guarantee that no sudden jumps will happen. Unlike physical clocks, a monotonic clock does not measure time in the sense of current date or time, instead, it starts counting from some arbitrary moment, for example, the moment an operation begins. A monotonic clock aids with computing the elapsed time between two events observed on a single node. <br>

The downside of a monotonic clock is that it is only suitable for measuring elapsed time on a single node. Computing monotonic clock timestamps across different nodes in a distributed system would not make much sense. <br>

### Logical Clock

In a distributed system, we look at time from a different angle. Instead of measuring time in seconds, we focus on accurately capturing the order of events. Formally, this is known as **logical clock**. <br>

To understand logical clocks, let's first look at an idea in computer science called **happens-before relation**. Happens-before is a relation that assumes, given two events, when one happens before the other, the output of the events must reflect this relationship. For example, if event _e1_ sends a message to event _e2_, then the message sent by _e1_ must have happened before it is received by _e2_ (e1 -> e2). <br>

This is a **partial order**, it is possible for some event _e1_ to have happened neither before nor after another event _e2_. In such case we assume _e1_ and _e2_ to be concurrent (e1 || e2). <br>

> By concurrent, we do not mean _e1_ and _e2_ happened at the same time, instead we assume that _e1_ and _e2_ are independent events.

Now that we understand the happens-before relationship, let's look at our first logical clock, **Lamport clock**.

#### Lamport Clock

A Lamport clock is a counter, responsible for counting the number of events that occurred and incrementing the counter with each occurrence. Each node in a distributed system has its own local Lamport counter, and the counter is incremented each time an operation is executed.
When a node communicates with another node (for example when sending a message), the sender would attach its local Lamport counter with the message, and the receiver would increment its counter when the message is received. <br>

![VectorClock.jpeg](https://cdn.hashnode.com/res/hashnode/image/upload/v1669003956887/M7_Oud5mS.jpeg align="left")

The Lamport clock guarantees that if an event _a_ happened before an event _b_, then _b_ will always have a greater Lamport timestamp than _a_. <br>
The limitation of the Lamport clock is that the converse of this property is not true, i.e. if the timestamp of an event _a_ is lower than the timestamp of another event _b_, then it is not possible to know if _a_ actually happened before _b_ or if they are concurrent. <br>

In our diagram, we can see that event _e_ has a lower timestamp than _c_ but _e_ did not happen before _c_. In this case, this implies that event _e_ and _c_ does not have a happens-before relationship i.e. they are concurrent (e || c). <br>

In the above case, we had information about all the events in all three nodes, so we were able to determine that _c_ || _e_, however, suppose, if we are only given two Lamport timestamps, _a_ and _b_, and _a_ is less than _b_, then we cannot really determine if _a_ happened before _b_ (a -> b), or if they are concurrent (a || b). In many cases, it would be useful to have this information, and for this reason, we have another logical clock called _vector clock_, which lets us determine if events are concurrent.

#### Vector Clock

Vector clock is similar to Lamport clock, in the sense that both employ counters - In a Lamport clock, we have a single counter, whereas in a Vector clock we have an array of counters. <br>
Let's understand vector clocks with an example, suppose there are three nodes in a distributed system _N1_, _N2_, and _N3_. Each node will have a vector timestamp implemented as _[ t<sub>N1</sub>, t<sub>N2</sub>, t<sub>N3</sub> ]_, where _t<sub>N1,</sub>_ is the timestamp for _N1_, _t<sub>N2</sub>_ is the timestamp for _N2_ and so on. <br>
Initially, each timestamp in the array is set to 0, and when an operation executes on a node, the timestamp corresponding to that specific event is incremented by 1. <br>

In our example, when _N1_, sends a message to _N2_, _N1_ would increment the timestamp _t<sub>N1</sub>_ by 1, and attach a copy of its array with the message. When _N2_ receives the message, it would merge its local array with the copy sent by _N1_ by taking the element-wise maximum of two arrays and then incrementing its own timestamp. <br>
**The diagram below should aid in understanding this algorithm.**

![VectorClock-final.jpeg](https://cdn.hashnode.com/res/hashnode/image/upload/v1669005394077/mejzSZB_S.jpeg align="left")

With Vector clocks we can define a partial order. The rules for defining this order are - <br>

Given we have two events _E1_ and _E2_, with _t<sub>E1</sub>_, and _t<sub>E2</sub>_ as the arrays of their vector timestamps.

- If every timestamp in _t<sub>E1</sub>_ is less than or equal to the corresponding timestamp in _t<sub>E2</sub>_, and there is at least one timestamp in _t<sub>E1</sub>_ lower than _t<sub>E2</sub>_, then _E1_ -> _E2_.** In our diagram, _b_ -> _c_**.

- If we cannot meet the above requirements, then the events are considered to be concurrent. **In our diagram, _c || e_**. <br>

This wraps up our discussion of time and clocks in distributed systems. <br>
The concept of time can feel a bit abstract, but it forms the basis of distributed systems, and recognizing different types of clocks would aid us in the journey of understanding distributed systems.
