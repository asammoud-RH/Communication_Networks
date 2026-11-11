# Network Layers Visualizer

An interactive app that teaches network layers. It shows the OSI (Open Systems Interconnection) model and the four-layer and five-layer TCP/IP models side by side, then follows a message as each layer wraps it, as the receiver unwraps it, and as switches and routers read and rewrite it.

With it you can:
- map any layer of one model to the other two, and learn the other names each layer goes by;
- watch headers go on and come off, and count the bytes Wireshark shows on the sending and the receiving host;
- step one packet through two switches and a router and see which fields change, with and without NAT (Network Address Translation);
- place tricky protocols such as ARP (Address Resolution Protocol), ICMP (Internet Control Message Protocol), and QUIC by what carries them;
- troubleshoot one layer at a time with Linux and Windows commands.

Three modes:
- **Learn:** a lesson map and six short lessons, each with a live figure and a quick task.
- **Calculator:** Ethernet frame sizes, padding, wire time, and efficiency for any message.
- **Challenge:** ten fresh questions at three levels, with badges and a completion code.

**How to open it:** download `network-layers-visualizer.html` and open it in any current browser. It is one file and works offline.

**Links into the app:** `?mode=learn|calc|challenge`, `&lesson=0..6` (0 is the lesson map), `&level=E|M|H`, and for the Calculator `&msg=<bytes>&l4=tcp|tcpts|udp&ip=4|6&tag=1`.

**Building from source:** `cat source/01-head.html source/02-core.js source/03-learn.js source/04-calc.js source/05-challenge.js > network-layers-visualizer.html`
