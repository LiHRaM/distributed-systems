# Topic 7: Consensus

* Explain the consensus problem
* Solution in synchronous system

* Explain the Byzantine generals problem.
* Present impossibility result for 3 Byzantine generals, 1 faulty (argue carefully!)
* Present the solution for 4 Byzantine generals, 1 faulty.
* Present clearly your assumptions on system model, failures, and message signing.
* Discuss impossibility in asynchronous systems and practical workarounds.

[DS5ed] 15.5

## The consensus problem
* **In multi agent systems, a way to ensure that the transactions and data are reliable, even in the face of faulty processes.
* ** Faulty processes can be:
  * Individual processes failing
    * Crashes or omissions
  * Byzantine ( = Arbitrary) failures
    * Inconsistent outputs, corrupted local state, etc.
  * No message signing
    * A processes I makes false claims about the information it got from process J.

* ** Redundancy
  * Increase the amount of data that exists, adding more input to the decision making process.

* ** Consensus algorithm for 3 processes:
  * each process propose a value, V
  * The consensus algorithm broadcasts the decided upon value, D.

* ** Consensus algorithms require the following:
  * ** **Termination**, eventually each correct process P_i sets its decision variable D_i
  * ** **Agreement**, the decision variable value of all correct processes are the same: if P_i and P_j are correct and have entered their decided state, then D_i = D_j (for all i,j ∈ 1..N)
  * ** **Integrity**, if the correct processes all proposed the same value, then any correct process in the decided state has chosen that value.
  * 
  * ** **Weak integrity**, the agreed value must be one proposed by a correct process. (ER IKKE SIKKER PÅ DET HER)

## Solution to the consensus problem in a synchronous system.
Very simple, if no failures:
* ** Each process multicasts its value to all processes
* ** Waits until a values are received from all processes.
* ** Makes a decision.

EVENTUELT HAV EN DOODLE MED OM MULTICAST EXAMPLE.

* ** F-resilient consensus algorithm
  * ** Solves consensus for f failed proceses
* ** Example: Several rounds
  * ** Round 1:
    * Each process multicasts its value
    * Wait for values from other processes
  * ** Round 2
    * Each process mutlicasts the values received from other processes in round 1
    * Wait for values
  * Repeat, only sending new values. 
* Requires atleast f+1 rounds, f being the number of failed processes.
* When a round is reached with no failure, every process would decide on the same value.


## Byzantine generals problem
* ** Is based around the turkish invasion into Byzantium.
* ** generals must agree to *attack* or *retreat*, while only being able to communicate by messengers.
* ** Messages are safe, they cannot be intercepted or tampered with.
* ** Traitor generals can exist
  * Traitors can omit answering messages from generals
  * Send arbitrary messages
    * Byzantine failures = arbitrary faults including:
      * Omission failures, crash failures, send different values to different processes
      * Being intentionally malicious, buggy
* ** Attacking with only one army means defeat.
### Cost of Byzantine Generals
* ** Requires f+1 rounds, in the general case, f>=1
* ** Sends O(N^f+n) messages.
  * ** If using digital signatures, a solution exist with only O(N^2) messages, still f+1 rounds)
    * If a process says another process said something else, it can be detected.
* ** As cost is high, only use if threat is great.
* ** If the source of failures is known, use specialized solutions instead.

### Impossibility result for the 3 byzantine generals problem, 1 faulty
**EVENTUELT HAV EN DOODLE MED OM N=3**
* ** There are not f-resilient algorithm if the number of processes are N<=3
* * ** Shows that no consensus algorithm for N>3f exists.
* ** If the commanding process is correct, but one of the receives are faulty, the other receiver would receive conflicting information
  *  P1 says V, P2 says P1 said W, P3 received conflict.
* If the commanding process, the one broadcasting the order is the traitor
  * P2 and P3 received different information

* ** A consensus algorithm cannot distinguish between the above scenarious, meaning that both Integrity and Agreement cannot be achieved

### Solution for 4 Byzantine generals problem, 1 faulty.
**Eventuelt have en doodle med foor N=4**
* ** For 4 processes the result is different:
* ** If the issuer, P1 is correct, but P4 is false.:
  * The P2 and P3 will both say that P1 said the same thing, meaning they can reach consensus
* ** If the issuer, P1 is a traitor/ faulty:
  * P2, P3, P4 will receive different values, and will be able to decide on a default value instead

## Assumptions about the system model, failures, and message signing. (HAR ingen ide om hvad der skal være her? Føler det lidt er svaret andet steds?)

## Impossibility in asynchronous systems and practical workarounds.
* ** No algorithm can exist that reaches consensus in asynchronous systems.
  * ** Even if we allow 1 crash without byzantine failures or communication failures
    * ** Failed processes cannot be distinguished from slow processes.
    * ** There is always a program continuation that avoids consensus
  * ** Consensus can be reached, but not guaranteed.

### Paxos
* Paxos is named after the Parliament on the fictitious Greek island of Paxos.
* Paxos is a family of algorithms (by Leslie Lamport) for distributed consensus in an asynchronous system
* ** In Paxos termination / liveness is not guaranteed, but happens in “reasonable environments”
* * ** Different roles exist:
  * Proposer: Offers proposals, with multiple proposers at once, they instead compete to reach approval first.
  * Acceptors: Accepts or rejects proposals
  * Learners: Simply learns the agreed upon proposals
* Proposals must have majority to be accepted.
* ** Paxos Consensus works by sending a prepare request to some acceptors (other participants of the blockchain)
  * ** The acceptors accept the proposal
  * ** The proposer sends a commit request
  * ** The acceptors accept the commit.
* ** Paxos only requires a majority to accept, meaning half the participants of the blockchain can never answer, and Paxos will still work.


### Workarounds in asynchronous systems (denne var et slide i svinet)
* ** Masking faults
  * ** Several options:
    * ** Restart crashed processes and use persistent storage
    * ** Recovery files
    * ** Save state-snapshots
* ** Use failure detectors
  * ** Make failure fail-silent by discarding messages
*  ** Probabilitistc algorithms (randomization):
   *  Conceal strategy from opposition / Advesary.
* ** Nearly synchronous systems:
  * Uses Paxos Algorithm '98, completely-safe and largely-live consensus protocol for asynchronous systems.
  * Used "part-time parliament" analogy but took 9 years before it was published, updated in "Paxos made simple" paper.
  * "Asynchronous consensus algorithms like Paxos maintain safety despite asynchrony, but are guaranteed to make progress only when the system becomes synchronous - meaning that messages are delivered in a bounded length of time.“