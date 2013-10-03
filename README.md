go-fish
=======

a toy distributed NoSQL database to be written in Go.

possible features
-----------------
* key/value store
* secondary indexing
* JSON / BSON API

objectives
----------
* fun to hack on
* distributed
* durable
* higher availability
* partition tolerant
* eventually consistent
* easy to manage
* maybe fast, someday

components
----------
* distributed hash table / messaging bus
* key-value store
* JSON REST API

stuff to consider
-----------------
* how and where to store secondary indexes
* compaction
* [hinted handoff](http://www.datastax.com/dev/blog/modern-hinted-handoff)
* active anti-entropy
* read repair
* conflict resolution

references
----------
* [go-json-rest](https://github.com/ant0ine/go-json-rest), a REST server framework with [a blog post](http://blog.ant0ine.com/typepad/2013/04/introducing-go-json-rest.html)
* a [skip list](https://bitbucket.org/ede/go-skiplist) or a [red-black tree](https://github.com/petar/GoLLRB)
* [snappy](https://code.google.com/p/snappy-go/) for compressing values?
* [wendy](https://github.com/secondbit/wendy/) - another Go DHT
* [vclock](https://labix.org/vclock) - vector clocks for Go

discarded options
-----------------
* [gocask](https://code.google.com/p/gocask/) provides an (incomplete) implementation of Bitcask
* [leveldb-go](https://code.google.com/p/leveldb-go/) - another option for disk store, but not mature yet
* [dht](https://github.com/nictuku/dht) - a distributed hash table implementation in Go, but too focused on BitTorrent?

