go-fish
=======

A data structure server to be written in Go.

Why Go? Because it looks fun.

Why a data structure server? Because a certain existing data structure server is fast and convenient, but also single-threaded, difficult to cluster, and doesn't provide an architecture to plug in new data structures.

objectives
----------
* fun to hack on
* Redis protocol compatible
* pluggable data structures
* distributed
* durable
* higher availability
* tolerant to network partition
* easy to manage
* maybe useful, someday

design ideas
------------

### Raft as a starting point ###

[raftd](https://github.com/goraft/raftd) provides a scaffold for a network service that relies on the [Raft algorithm](https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf) to maintain consistency between nodes. The [goraft](https://github.com/goraft/raft) library supports an extensible command set and a pluggable transport layer.

Any write operation written to any node in the cluster would be forwarded on to the current Raft master. The Raft algorithm would ensure that all data writes  are strictly ordered in time and propagated identically to every node. Each peer would simply need to update its data structures whenever it receives a new log entry from the current master.

For performance, the cluster could be configured to wait for confirmation of the write or to not wait before returning, according to the needs of the application.

Since each peer in the cluster would have a complete copy of the write log, any peer could, in theory, maintain a copy of the current version of every data structure. At the outset, this means that we can load balance all read and write requests to any peer in the cluster.

The application of the Raft algorithm in this fashion implies that a go-fish cluster would sustain the loss of up to (*n*/2)+1 nodes at any time, for all *n* > 2, without loss of uptime.

The existence and clean design of the goraft library makes this a very straightforward proposition.

### partitioning ###

At the outset, we'd implement a single partition of the entire data on every node for simplicity. However, we want to design against the point in time where we'd like to store more data than fits in memory on a single machine. Proper partitioning will allow us to use the entire memory allocation of the cluster, divided by a replication factor, as the available space for hosting data structures.

We can divide the data into some constant number of partitions, each containing its own copy of every data structure type (e.g. strings, hashes, lists, etc). We assign any data entry to a partition by taking a hash of the key (regardless of structure type), modulo the number of partitions.

Each peer would host a number of partitions equal to *p* / *n* x *r*, where *p* is the number of partitions and *r* a configuration-dependent read replication factor.

Each partition would continue to be available as long at least one of its hosts were available. However, since every peer records the entirety of the write log, any peer can recreate any partition from its copy of the log, without fetching additional data off the network. This is probably okay because disk space is always cheaper than memory.

Every peer would maintain a list of the *r* peers known to be hosting each of the *p* partitions at the current time. Any read request sent to a peer *not* hosting the data in the requested partition proxy the request to one or more of the peers currently hosting it.

Whenever a new Raft leader is elected, it would check each partition to ensure that *n* hosts for that partition are alive and well. If not, it would select a requisite number of new hosts by means of a consistent hash, and issue a (logged) command instructing those peers to begin building the partition from their copies of the log. Once a peer completes the reconstruction of the partition, it issues a command advising the cluster that it henceforth hosts a live copy of that partition and other peers can proxy read requests to it.

Eventually, peers could in principle checkpoint whole partitions to disk in order to speed up partition recovery.

possible next steps
--------------
* A parser / serializer for the [Redis wire protocol](http://redis.io/topics/protocol). Very possibly an existing [Redis client library for Go](http://redis.io/clients#Go) could be appropriated.
* A [Raft transport layer](https://github.com/goraft/raft/blob/master/http_transporter.go) that uses the protocol layer.
* A partition structure with a plugin interface to allow data struct modules to register themselves, and the commands they support, with the server.
* A dispatch layer that receives commands over the wire and dispatches them to the appropriate plugin for handling.
* A first plugin implementing the GET/SET/INCR commands, &c.
* Some kind of assignment process that distributes responsibility for handling each partition around the cluster.
* Proxy routines for forwarding read requests to a responsible peer.

stuff to consider
-----------------
Log persistence. Log compaction. Partition snapshots.

references
----------
* [goraft](https://github.com/goraft/raft) - an implementation of [Raft](https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf) in Go.
* [doozer](https://github.com/ha/doozerd) - highly available leaderless consistent data store (uses goraft)
* a [skip list](https://bitbucket.org/ede/go-skiplist) or a [red-black tree](https://github.com/petar/GoLLRB) for indexing keys in memory
* [snappy](https://code.google.com/p/snappy-go/) for compressing values?
* [wendy](https://github.com/secondbit/wendy/) - another Go DHT
* [vclock](https://labix.org/vclock) - vector clocks for Go
* [hashring](https://github.com/warlockcc/golibs/tree/master/hashring) - consistent hashing implementation
* [tiedot](https://github.com/HouzuoGuo/tiedot) - json document database (also embeddable)
* [gocask](https://code.google.com/p/gocask/) provides an (incomplete) implementation of Bitcask
* [leveldb-go](https://code.google.com/p/leveldb-go/) - another option for disk store, but not mature yet
* [dht](https://github.com/nictuku/dht) - a distributed hash table implementation in Go, but too focused on BitTorrent?
* [consistent](https://github.com/stathat/consistent) - consistent hashing implementation, but it uses CRC32 as a "hash"
* [go-json-rest](https://github.com/ant0ine/go-json-rest), a REST server framework with [a blog post](http://blog.ant0ine.com/typepad/2013/04/introducing-go-json-rest.html)
