# Subnet Visualizer

An interactive, single-page app for learning IPv4 (Internet Protocol version 4) subnetting by seeing it. Change an address or a prefix length and the figures, the subnet math, and the ping results update right away.

- See how the prefix length splits an address into network bits and host bits.
- Find the network address, broadcast address, host range, and host count.
- Watch ARP (Address Resolution Protocol) and ping, step by step, between hosts whose subnets overlap.
- Put one host in two subnets, and see which address it sends from.
- Get the Linux and Windows commands that join and leave a subnet.

## Modes

- **Learn:** six short lessons, each with an animated figure and a small task.
- **Calculator:** type any address and prefix length (or mask) to see its subnet, overlaps, and block split.
- **Challenge:** timed or ten-question rounds at three levels, with hints, badges, and a completion code.

## How to use it

Download `subnet-visualizer.html` and open it in any modern browser. It works offline and needs no installation.

Link parameters: `?mode=learn|calc|challenge`, `lesson=0..6`, `ip=192.168.10.130/25`, `level=E|M|H`, `net=192.168.20`. Example: `subnet-visualizer.html?mode=learn&lesson=4`.

<!-- layer: network -->
