# dhtproxy

This is a proxy that accepts BitTorrent tracker [announce requests](https://wiki.theory.org/BitTorrent_Tracker_Protocol) over HTTP and converts them to [mainline DHT](https://en.wikipedia.org/wiki/Mainline_DHT) lookups.  This allows clients which are unable to use DHT to bootstrap some peers in a trackerless swarm, after which it can hopefully use [PeX](https://en.wikipedia.org/wiki/Peer_exchange) to find more.

#### Usage

* [Install](https://golang.org/doc/install) Go.
* ```go get github.com/die-net/dhtproxy```
* ```./dhtproxy -listen=:6969```
* Point your BitTorrent client at http://127.0.0.1:6969/announce

#### Limitations

* Is read-only from the DHT.  It doesn't record "announce" information from its clients and share it with either the DHT or other clients of the dhtproxy.  If too much of a swarm is behind dhtproxy, the nodes won't be able to find each other.
* All DHT nodes are returned as having an incomplete copy of the torrent data, thus clients will show all DHT peers as "leeches". This is cosmetic-only; clients will still be able to use seeds normally when they connect to them.
* Only supports the "compact" tracker protocol, and returns an error if a client tries to use the non-compact protocol. This would be easy to fix if there are clients that don't support the compact protocol.
* Uses [nictuku's DHT implementation](github.com/nictuku/dht) whose API isn't well suited to this task. Consequently, dhtproxy may have trouble picking up new additions to the DHT for a particular infohash, and ends up using more memory than would be ideal.  A temporary workaround is to restart dhtproxy occasionally.
