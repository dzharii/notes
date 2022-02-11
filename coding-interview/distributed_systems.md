## Theory

- [Reading Group. Paxos vs Raft: Have we reached consensus on distributed consensus? | Aleksey Charapko](http://charap.co/reading-group-paxos-vs-raft-have-we-reached-consensus-on-distributed-consensus/)
- [Cloud Spanner: TrueTime and external consistency  |  Google Cloud](https://cloud.google.com/spanner/docs/true-time-external-consistency)
- [Signals and Threads | Clock Synchronization](https://signalsandthreads.com/clock-synchronization/)
- [Paper review: Paxos vs Raft](https://emptysqua.re/blog/paxos-vs-raft/)

Heidi Howard, the first author of this paper did two videos about her paper:
    - A 10' short intro https://www.youtube.com/watch?v=JQss0uQUc6o
    - A more in depth one : https://www.youtube.com/watch?v=0K6kt39wyH0
- [Paxos lecture  Raft user study  - YouTube](https://www.youtube.com/watch?v=JEpsBg0AO6o)
- [Consensus in the Cloud: Paxos Systems Demystified](https://muratbuffalo.blogspot.com/2016/04/consensus-in-cloud-paxos-systems.html?m=1)
- [ 2008.13456  Lecture Notes on Leader-based Sequence Paxos -- An Understandable Sequence Consensus Algorithm](https://arxiv.org/abs/2008.13456)
- [ailidani/paxi: Paxos protocol framework](https://github.com/ailidani/paxi) Go lang
- [P: A programming language designed for asynchrony, fault-tolerance and uncertainty - Microsoft Research](https://www.microsoft.com/en-us/research/blog/p-programming-language-asynchrony/)
- 

## Testing 
- [Testing Distributed Systems](https://asatarin.github.io/testing-distributed-systems/) Curated list of resources on testing distributed systems

## Redis 

- [Last Mile Redis · Fly](https://fly.io/blog/last-mile-redis/)

- [Last Mile Redis | Hacker News](https://news.ycombinator.com/item?id=27861510)
    We do this to locally distribute our javascript… from our main rails app we publish out to each region in aws where we have a cluster of redis and nginx and a little bit of lua to grab and render data out… it’s super fast and very easy to maintain … you get your nice normalized database with all its complex queries and then on the client side you get your nice big blob of context all precomputed and perfect for rendering exactly what the client needs… bonus you cache the context data for a shorter time in each local nginx worker for even faster response times then serving from disk…


## Postgress

- [Globally Distributed Postgres · Fly](https://fly.io/blog/globally-distributed-postgres/)