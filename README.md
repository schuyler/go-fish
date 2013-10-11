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
[raftd](https://github.com/goraft/raftd) provides a scaffold for a network service that relies on the [Raft algorithm](https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf) to maintain consistency between nodes. The [goraft](https://github.com/goraft/raft) library supports an extensible command set and a pluggable transport layer.

Next steps would include implementing:

* A parser / serializer for the [Redis wire protocol](http://redis.io/topics/protocol). Very possibly an existing [Redis client library for Go](http://redis.io/clients#Go) could be appropriated.
* A [Raft transport layer](https://github.com/goraft/raft/blob/master/http_transporter.go) that uses the protocol layer.
* A partition structure with a plugin interface to allow data struct modules to register themselves, and the commands they support, with the server.
* A dispatch layer that receives commands over the wire and dispatches them to the appropriate plugin for handling.
* A first plugin implementing the GET/SET/INCR commands, &c.
* Some kind of assignment process that distributes responsibility for handling each partition around the cluster.
* Proxy routines for forwarding read requests to a responsible peer.

stuff to consider
-----------------
* log structured persistence. snapshots. log compaction.
* [hinted handoff](http://www.datastax.com/dev/blog/modern-hinted-handoff)?

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
