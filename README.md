# rr-dial-peer

This is the same as the [rr-dial
example](https://github.com/dhuseby/rr-dial.git) except that rather than use
mDNS to discover other peers, it directly dials them.

## Run the Listening Peer

By default the peer runs as a listening peer.

```sh
$ cargo run
```

You'll see output like the following:

```sh
local peer id: 12D3KooWP3GiWRRXPwtwogmSz8u9jpqgcnZELa3EQF47p1usB9jU
Local peer is listening on /ip4/127.0.0.1/udp/58582/quic-v1
```

Copy and the multiaddr string on the second line (e.g. "/ip4/127.0.0.1/udp/58582/quic-v1").

## Run the Dialing Peer

To run it as a dialing peer, just provide the multiaddr of a listening peer like so:

```sh
$ cargo run -- /ip4/127.0.0.1/udp/58582/quic-v1
```

The listening peer gets a request from the dialing peer every 10 seconds so if
you let them run for 20 seconds you should see output like the following:

```sh
Successfully received dial from 12D3KooWT36359Sve7doiQVwb9CWk8t88jgfcGtTL19ALdC5bqSY:/ip4/127.0.0.1/udp/37899/quic-v1
Peer 12D3KooWT36359Sve7doiQVwb9CWk8t88jgfcGtTL19ALdC5bqSY speaks our protocol
received request Hello from
received request Hello from /ip4/127.0.0.1/udp/34648/quic-v1
```

You'll notice that the first "received request..." like doesn't contain the
multiaddr for the dialing peer. That is because the listening peer is just
printing out a string that was baked on the dialing peer's side. The dialing
peer doesn't know what its multiaddr is until it receives a reply back from the
listening peer with the multiaddr the listening peer sees the dialing peer at.
The second request from the dialing peer then has its multiaddr as seen from
the listening peer.

On the dialing peer, if the dial is successfull you'll see output like the following:

```sh
local peer id: 12D3KooWBmNo8DuZ1EaBhZ9xXLpuwrHszZcspqang5r4nqLDHPvb
Dialed /ip4/127.0.0.1/udp/42755/quic-v1
Greeting 0 Peers!
Successfully dialt to 12D3KooWQZXHx3rGUXEGScce5xKdc8E4csoyqusFrNRTUESVxomw:/ip4/127.0.0.1/udp/42755/quic-v1
Peer 12D3KooWQZXHx3rGUXEGScce5xKdc8E4csoyqusFrNRTUESVxomw speaks our protocol
Greeting 1 Peers!
Greeting: 12D3KooWQZXHx3rGUXEGScce5xKdc8E4csoyqusFrNRTUESVxomw
received response: Hello back from /ip4/10.137.0.19/udp/42755/quic-v1
Greeting 1 Peers!
Greeting: 12D3KooWQZXHx3rGUXEGScce5xKdc8E4csoyqusFrNRTUESVxomw
received response: Hello back from /ip4/10.137.0.19/udp/42755/quic-v1
```

It first lists its peer id and reports the multiaddr it dialed. After the
successful dial, the peers run the `Identify` protocol and check to see if they
are the same agent version. Ideally this check would be more sophisticated and
allow for different agent versions as long as they spoke compatible protocol
versions. It could even include a custom protocol whereby the listening and
dialing peers negotiate which protocol this connection will speak. But that is
left as an exercise for the reader.

Once the dialing peer is convinced that the listening peer speaks the same
protocol, it sends a request to the listening peer every 10 seconds and prints
out the reply it receives.
