![[Pasted image 20240601181226.png | center | 400]]

- Reliability: Continuing to work correctly, even when things go wrong.
- Scalability: The ability of a system to cope with increased load.
- Maintainability: Making life easy for operations, managing complexity, making change easy

### Reliability
1. The application performs the function that the user expected.
2. It can tolerate the user making mistakes.
3. Its performance is good enough for the required use case, under the expected load and data volume.
4. The system prevents any unauthorised access and abuse.



### Scalability
Scalability is the term we use to describe a system’s ability to cope with increased load. Load can be described with a few numbers which we call load parameters, for example 
- Requests per second to a web server.  
- The ratio of read and write in a database.
- The number of simultaneously active users in a chat room. 
- The hit rate in a cache.


#### Performance
OLAP Systems: Throughput(The number of records we can process per second)
OLTP Systems: Response Time (The time between a client sending a request and receiving a response.

Arithmetic mean vs Median, Percentiles. Tail latencies: High percentiles of response times.
Amazon describes response time requirements for internal services in terms of the 99.9th percentile. 

100ms increase in time $\approx$ 1% sale, 
![[Pasted image 20240601182611.png | center | 500]]
#### Improve scalability
- Scale up (vertical scaling) moving to a more powerful machine.  
- Scale out (horizontal scaling) distributing the load across multiple small machines

Shared-nothing architecture
- In reality, good architecture usually involve a pragmatic mixture of approaches.
-  An architecture that scales well for one application is built upon assumptions of which operations will be common and which will be rare — the load parameters. If those assumptions turn out to be wrong, the engineering effort for scaling is at best wasted, and at worst counterproductive.

### Maintainability
Ongoing maintenance, for example

Fixing bugs, keeping systems operational, investigating failures, adapting them to new platforms, modifying them for new use cases, and adding new features.

Design principles for legacy systems:  
- Operability  
- Simplicity  
- Evolvability (extensibility, modifiability, or plasticity)

#### Operability
Make it easy for operations teams to keep the system running smoothly, e.g.
- Provide visibility into the runtime behavior and internals of the system with good monitoring.
- Provide good support for automation and integration with standard tools.
- Provide good documentation.
- Provide good default behaviour.
- Exhibiting predictable behaviour, minimising surprises.

#### Simplicity
Make it easy for new engineers to understand the system, by managing complexity, e.g.
- Explosion of the state space
- Tight coupling of modules
- Tangled dependencies
- Inconsistent naming and terminology
- Hard to understand / reason
- Hidden assumptions
- Unintended consequences
- Unexpected interactions

Good example: SQL

Good abstraction that hides complex on-disk and in-memory data structures, concurrent requests from other clients, and inconsistencies after crashes.

#### Evolvability
Make it easy for engineers to make changes to the system in the future, e.g., 
- New facts  
- New use cases  
- New business priorities
- New features requested  
- New regulations  
- Growing load requests architectural changes