# TCP Visualizer

An interactive, single-page app that shows how TCP (Transmission Control Protocol) delivers every byte, once and in order, over a network that loses, delays, and reorders packets. Step through each connection on a timeline and watch both hosts move through the TCP state diagram.

- Read every field of a TCP header and the frame size Wireshark shows.
- Follow the three-way handshake and its sequence (Seq) and acknowledgment (Ack) numbers, raw or relative.
- See flow control at work: the receiver's window, a zero window, and window probes.
- Find a lost segment from duplicate ACKs (acknowledgments) or a timeout, with and without SACK (Selective Acknowledgment).
- Compute the retransmission timeout (RTO) from round-trip time samples.
- Close a connection from either side, and take every arrow of the state diagram.
- Get the Linux and Windows commands that show the same things on a real computer.

## Modes

- **Learn:** eight short lessons in four parts, each with step-by-step figures and a small task.
- **Calculator:** build any connection, lose any segment, and see every segment, both hosts' states, and a Wireshark-style capture; plus quick calculators.
- **Challenge:** rounds at three levels, with hints, badges, and a completion code.

## How to use it

Download `tcp-visualizer.html` and open it in any modern browser. It works offline and needs no installation.

Link parameters: `?mode=learn|calc|challenge`, `lesson=1..8` or `lesson=map`, `level=E|M|H`, `os=linux|windows`; in the Calculator also `preset`, `lose`, and `raw=1`. Example: `tcp-visualizer.html?mode=calc&preset=fast&lose=5-9`.

The app is kept as six source parts; to rebuild it:

```
cat source/01-head.html source/02-sim.js source/03-core.js source/04-learn.js source/05-calc.js source/06-challenge.js > tcp-visualizer.html
```
