# BFT-Bench: Towards a Practical Evaluation of Robustness and Effectiveness of BFT Protocols

Citation:

```

@inproceedings{Gupta:2016:BTP:2958606.2958616,
 author = {Gupta, Divya and Perronne, Lucas and Bouchenak, Sara},
 title = {BFT-Bench: Towards a Practical Evaluation of Robustness and Effectiveness of BFT Protocols},
 booktitle = {Proceedings of the 16th IFIP WG 6.1 International Conference on Distributed Applications and Interoperable Systems - Volume 9687},
 year = {2016},
 isbn = {978-3-319-39576-0},
 pages = {115--128},
 numpages = {14},
 url = {https://doi.org/10.1007/978-3-319-39577-7_10},
 doi = {10.1007/978-3-319-39577-7_10},
 acmid = {2958616},
 publisher = {Springer-Verlag New York, Inc.},
 address = {New York, NY, USA},
 keywords = {Benchmarking, Byzantine faults, Fault injection, Fault tolerance, Performance, Robustness},
} 


```

---

## Summary

### Motivation:

- No existing BFT algorithm benchmarking framework.
- There are many existing BFT algorithms that have been developed.

### Problem:

- BFT algorithms are complex to implement, there have been many algorithms developed.
- There is no way of comparing and evaluating the performance of BFT algorithms in practice.

### Contribution:

- The **FIRST** BFT benchmarking framework.
- Framework tests a number of implementations.
- Also tests using a range of different byzantine fault behaviours.

---

## Intro

* Large amount of work to improve performance with message rounds and crypto operations.
* Many proposals for specific type of fault being handled.
* BFT algorithms are too difficult/complex to implement for a general case.
	* Implementing each time for a new protocol must be compared to existing.

## Related

* BFT Performance Improvement in Fault-Free Conditions:
	* Cost of BFT is the main drawback.
	* If fault free scenario = use lightweight protocol.
	* Else, switch to robust protocol.
	* Examples:
		* Zyzzyva
		* Aliph
		* Chain
* BFT Performance Improvement in Presence of Faults
	* Approach is improving for practical and efficient mechanisms.
	* Examples:
		* Aardvark
		* Prime
		* RBFT
* BFT Simulation and Benchmarking.
	* Most current benchmarks rely on underlying structure/framework.
		* E.g. MapReduce or Web Servers.
	* Benchmarking environment simulates the client and only focuses on BFT.

## BFT Bench Framework

* PBFT will undergo the viewchange if there is problems with the primary.
* Chain cannot provide BFT on it's own - it relies on different protocols.
* Aliph modifies the protocol it uses to suit the situation.
	* Quorum if **no** contention and **no** faults.
	* Chain if **no** faults but contention is present.
	* PBFT if contention and faults.
* Protocols enhancing performance in presence of faults:
	* Aaradvark is resilient against intentional flooding / delay / corruption.
		* It uses a replica blacklist OR digital signature.
	* RBFT 
		* Concurrent messages.
		* Only executed one-by-one on the *master*.
		* If performance is bad then new master.

### Fault Types

* **Replica Crash**
	* Replica stops and does not participate.
	* Bench remotely kills the process.
	* E.g. like mute, does not communicate.
* **Message Delay**
	* Implements a sleep before sending the message.
* **Network Flooding**
	* Sends multiple messages of chosen sizes to other replicas.
* **System Overload**
	* Number of concurrent clients/accesses increases.

### Faultload

* Faultload is the description of what happens in the experiment.
	* Type of file outlining parameters.
* The central injector node will communicate with a replica to trigger the fault.
	* It looks at the parameters (e.g. time, which replica, what type of fault etc.) specified in the file
* Client requests are handled in ``FIFO`` manner.
* BFT Bench will simulate clients and multi-client usage.

### Monitoring

* Latency = time between client request and receiving a response.
* Throughput = number of requests / second.
* The monitoring also will look at CPU load, RAM, Network I/O.

### Extensibility

* Most of the normal fault types (e.g. crash..) are simple plug-and-play since it is independent of protocol.
* Other faults/interactions will need to modify the underlying BFT protocol.

## Experimental Setup

* BFT Protocols:
	* PBFT
	* CHain
	* RBFT
	* Zyzzyva
	* Aliph
	* Aardvark
* Nodes:
	* N = 4
	* f = 1
	* +2 for bench/injector and concurrent client manager.
* Processing time was simulated at 100 microseconds.
* **Warm up phase = 180seconds**.

### Evaluation

#### Crash

* PBFT = new viewchange.
* Aliph = same as PBFT (switches protocol)
* CHain = can't propagate any messages - needs to switch to PBFT but not implemented in this way.
* Zyzzyva does not implement a crash tolerant solution
* Aardvark + RBFT = this fault isn't covered.

#### Message Delays

* Aardvark, PBFT, RBFT, Zyzzyva = delay introduced at the primary.
* Chain = delay introduced at the head replica.
* Aliph = has no primary so a random replica was chosen.

Results:

* RBFT tolerates the delays smoothly, minor adjustment
* All others increase significantly:
	* Aardvark = increase by 1 order of magnitude.
	* Others (- RBFT and Aardvark)  = increase by 2 orders of magnitude

#### Network Flooding

* Replica starts sending messages after a given delay.

Results:

* Aardvark + RBFT = resilient to this.
* PBFT has **bad** performance.
* Aliph switches to PBFT and exhibits same behaviour.

#### Complex Scenario

* Number of concurrent clients increases over time
* Message delays are introduced.

Results:

* RBFT
	* Tolerates not too high contention
	* At 10 clients (concurrent) - it terminates.
* Aardvark
	* Performance overhead to keep running
	* Handles contention smnoothly at low numbers
	* 10 clients - crashes.
* Zyzzyva + Chain
	* Able to face delay but significant performance drop (2 orders of magnitude)
	* 10 clients:
		* Chain = very slow but still able to cope
		* Zyzzyva = crash
* PBFT + Aliph:
	* Drops latency to 2 orders of magnitude instantly
	* Replaces fault replica - activates viewchange.
		* Aliph switches to use PBFT.
