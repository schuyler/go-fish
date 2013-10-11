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
* partition tolerant
* eventually consistent
* easy to manage
* maybe useful, someday

stuff to consider
-----------------
* how is consistency maintained between nodes?
* how does node discovery work?
* log structured persistence. snapshots. log compaction.
* [hinted handoff](http://www.datastax.com/dev/blog/modern-hinted-handoff)
* active anti-entropy
* read repair
* conflict resolution: timestamps? vector clocks?

references
----------
* a [skip list](https://bitbucket.org/ede/go-skiplist) or a [red-black tree](https://github.com/petar/GoLLRB) for indexing keys in memory
* [snappy](https://code.google.com/p/snappy-go/) for compressing values?
* [wendy](https://github.com/secondbit/wendy/) - another Go DHT
* [vclock](https://labix.org/vclock) - vector clocks for Go
* [hashring](https://github.com/warlockcc/golibs/tree/master/hashring) - consistent hashing implementation
* [goraft](https://github.com/goraft/raft) - an implementation of [Raft](https://ramcloud.stanford.edu/wiki/download/attachments/11370504/raft.pdf) in Go.
* [doozer](https://github.com/ha/doozerd) - highly available leaderless consistent data store
* [tiedot](https://github.com/HouzuoGuo/tiedot) - json document database (also embeddable)
 
discarded options
-----------------

Most of these options relate to the earlier conception of go-fish as a NoSQL store.

* [gocask](https://code.google.com/p/gocask/) provides an (incomplete) implementation of Bitcask
* [leveldb-go](https://code.google.com/p/leveldb-go/) - another option for disk store, but not mature yet
* [dht](https://github.com/nictuku/dht) - a distributed hash table implementation in Go, but too focused on BitTorrent?
* [consistent](https://github.com/stathat/consistent) - consistent hashing implementation, but it uses CRC32 as a "hash"
* [go-json-rest](https://github.com/ant0ine/go-json-rest), a REST server framework with [a blog post](http://blog.ant0ine.com/typepad/2013/04/introducing-go-json-rest.html)
