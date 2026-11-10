# Fragmentation Visualizer

A standalone app that teaches how IP (Internet Protocol) fragments a datagram when a link's MTU (Maximum Transmission Unit) is too small, and how hosts avoid fragmentation. It is an independent tool, not attached to the textbook or the course labs. It follows `../App Requirements.md` and `../App Template Guide.md` and was started from `../app-template.html`. The hosting steps are in `../Deployment Guide.md`. Version 1.2, November 2026 (1.1 added the Lesson 6 animation speed slider and moved the ICMP label onto its own line above its arrow; 1.2 made 0.5× the slider's default, because 1× was too fast).

## Files

The app is one standalone file, `fragmentation-visualizer.html` (about 127 KB). The project keeps it as five source parts in `source/`, because the project tool cannot upload a file that large in one piece. To rebuild the single file, join the parts in order:

```
cat source/01-head.html source/02-core.js source/03-learn.js source/04-calc.js source/05-challenge.js > fragmentation-visualizer.html
```

On Windows, use `copy /b source\01-head.html + source\02-core.js + source\03-learn.js + source\04-calc.js + source\05-challenge.js fragmentation-visualizer.html`. To edit the app, change the part that holds the code (the theme and page are in 01, the model and figures in 02, the lessons in 03, the calculator in 04, and the Challenge and navigation in 05), then rebuild and rerun the tests.

## Goal

Students learn to:
- tell whether a datagram fits a link's MTU;
- find the data bytes per fragment and the number of fragments;
- fill in each fragment's Ident, M (More fragments) flag, Offset, and Total Length;
- follow a datagram across routers that fragment it again on smaller links;
- explain reassembly at the destination, and why one lost fragment loses the whole datagram;
- use the DF (Don't Fragment) bit, ping, tracepath, and Wireshark to find a path's MTU.

The names are generic (Host A, R1, R2, Host B). The model's addresses are Host A 192.168.1.10, link k 192.168.k.0/24, and Host B .20 on the last link. The destination and the interface name can be edited in the commands panel.

## Modes

**Learn: six lessons.** Each has live figures and a Try it task worth 20 points the first time.
1. **What an MTU is.** A datagram drawn inside a link's frame, with a size slider and six link types (Ethernet 1500, PPPoE 1492, VPN tunnel 1400, a small point-to-point link 532, jumbo 9000, the IPv4 minimum 68). Task: the largest datagram that fits a given link.
2. **Cutting a datagram.** The data bar with the cuts, and "Show the steps": MTU − 20, round down to a multiple of 8, cut, last piece. Task: the data bytes in fragment 1 for a random MTU.
3. **The header fields.** The original and every fragment, with Ident, M, Offset, Total Length, and the data bytes each carries. Also explains why Offset counts 8-byte units (13 bits × 8 covers 65,535). Task: the Offset of fragment k, or the Total Length of the last fragment.
4. **Across an internetwork (the core lesson).**
   - Host A, R1, R2, Host B with an editable MTU on each link, and the fragments on every link drawn to scale. Send animates the fragments link by link, and routers that fragment flash.
   - Examples: 1400 data bytes over 1500, 532, 1500, and "fragment twice" (2980 over 1500, 1000, 576: 3 then 4 then 7 fragments, checked on a real Linux path).
   - Task: with 1400 data bytes and Link 2 at 1000, change only Link 3 so that exactly four fragments reach Host B (any MTU from 444 to 507 works).
5. **Reassembly and loss.** Host B's reassembly buffer fills as fragments arrive in a random order. The total size is known only when the M = 0 fragment arrives. "Lose one fragment" runs the 30-second timer (shown faster) and discards the datagram. Task: the Offset of the missing fragment, from a list of received fragments.
6. **Avoid it: DF and path MTU discovery.**
   - DF, the ICMP (Internet Control Message Protocol) "fragmentation needed" message (type 3, code 4), path MTU discovery, the TCP (Transmission Control Protocol) MSS (Maximum Segment Size), and an IPv6 note.
   - Host A, R1, and Host B, with a choice of Link 2 MTU and five commands (Linux default, -M do, -M dont; Windows default, -f). Run 3 pings animates each ping and prints the terminal output. An animation speed slider (🐢 0.25× to 3× 🐇, default 0.5×) sets how fast it runs and takes effect even mid-run.
   - The commands panel. Task: the largest ping payload that passes with DF set.

**Calculator:**
- Inputs: a size (a ping payload, a UDP (User Datagram Protocol) payload, or IP data), DF clear or set, and one to four links with their MTUs. There are presets for a small link in the middle (1500, 532, 1500), a path that fragments twice, a tunnel, and a single Ethernet link.
- Stat tiles: Total Length, path MTU, packets on Link 1 and at Host B, bytes and extra header bytes on the last link, efficiency, and the largest payload with DF set.
- The DF result in words, and for pings, the Linux and Windows terminal output.
- The path figure, fragment cards for the fragments that reach Host B, and every link fragment by fragment.
- The commands panel.

**Challenge:**
- **Easy:** fits or not, data per fragment, number of fragments, Offset from a byte position, ping Total Length.
- **Medium:** Total Length of the last fragment, Offset of fragment k, data bytes from Offset and Total Length, Wireshark's byte offset to the Offset field, the last fragment of a big ping, UDP fragments.
- **Hard:** fragments after re-fragmentation, their Offset fields, the missing fragment, the first line of `ping -M do`, the largest DF ping on a path, bytes on the last link, and what Linux's default ping prints (path MTU discovery).
- Ten questions or a two-minute sprint, hints (−3), explanations, streaks, stars, best scores, and a review list.
- **Badges:** First round, Perfect 10, On fire (5 in a row), Offset oracle, Path finder, Speed runner, Scholar (all six lessons).
- **Completion code:** `L<score>-<right>of<total>-XXXX`, checked under "For instructors" in the footer. The checksum uses the prefix FV, so codes from other apps don't pass here.

## Model (checked on a real Linux kernel)

- **The header** is 20 bytes with no options. A ping payload adds 8 bytes (ICMP), and a UDP payload adds 8 bytes (UDP).
- **One link:** if 20 + data ≤ MTU, nothing changes. Otherwise cut pieces of ⌊(MTU − 20)/8⌋ × 8 bytes until the rest fits in one fragment. The last fragment carries all the rest, up to MTU − 20 bytes, and need not be a multiple of 8. This is Linux's rule (ip_do_fragment), and Scapy's fragment() gives the same result. It differs from "divide and round up" only when MTU − 20 isn't a multiple of 8; for example, MTU 530 with 1014 data bytes gives 504 + 510, which was confirmed on the kernel.
- **A path:** every router splits each fragment that is too big on its own. Offsets count from the original data, and the last piece keeps its parent's M flag. Routers never reassemble.
- **DF:** the first link that is too small drops the datagram. On Host A's own link that is a local error. At a router, it's an ICMP message with the next link's MTU.
- **Linux ping (iputils)** sets DF by default and learns the path MTU. After a "Frag needed" message, later pings are fragmented by Host A. With -M do it prints `ping: local error: message too long, mtu=N`, and -M dont clears DF. Linux never caches a path MTU below 552 (net.ipv4.route.min_pmtu); below that it stops setting DF in default mode, and -M do keeps getting the router's message. Reassembly waits 30 seconds (net.ipv4.ipfrag_time).
- **Windows ping** clears DF by default, and -f sets it. A router's message prints `Reply from <router>: Packet needs to be fragmented but DF set.`, and later pings print the same text locally, as in published Windows output.
- **Wireshark:** the packet details show Fragment Offset in bytes (1480), but the `ip.frag_offset` filter compares 8-byte units (185). This was checked with tshark 4.2; check it once in the lab's Wireshark 4.6.4.

## Commands (checked against Microsoft documentation, iputils, and a live kernel)

- **Linux:**
  - `ip link show dev eth0`
  - `ping -c 3 -s N`, `ping -c 3 -M do -s N`, `ping -c 3 -M dont -s N`
  - `tracepath -n ADDR`, `ip route get ADDR`
  - `sudo ip link set dev eth0 mtu N`, undone with `mtu 1500`
  - `sudo ip route flush cache`, which forgets learned path MTUs (checked)
- **Windows:**
  - `netsh interface ipv4 show subinterfaces`
  - `ping -n 3 -l N`, `ping -n 3 -f -l N`
  - `netsh interface ipv4 set subinterface "Ethernet" mtu=N store=active` as administrator, undone with `mtu=1500 store=active`. `store=active` lasts until a restart.
- **Wireshark filters:** `ip.flags.mf == 1 || ip.frag_offset > 0`, `ip.flags.df == 1`, `icmp.type == 3 && icmp.code == 4`, `ip.frag_offset == 185`.

## Link parameters

- `mode=learn|calc|challenge`
- `lesson=1..6`
- `level=E|M|H`
- Calculator: `size=1392`, `kind=ping|udp|data`, `mtus=1500-532-1500` (one to four links), `df=1`

For example: `fragmentation-visualizer.html?mode=learn&lesson=4` or `fragmentation-visualizer.html?mode=calc&size=1392&kind=ping&mtus=1500-532-1500&df=1`.

## Tests (November 2026)

**Logic test** (`../testing/logic_test (fragmentation-visualizer).py`, run with `--kernel` as root):
- 3,000 single-link cases matched an independent Python model and Scapy's fragment().
- 1,500 random three-link paths matched the Python model, with data conserved, offsets on 8-byte boundaries, and exactly one M = 0.
- **Real kernel:** Host A, R1, R2, and Host B were built as Linux network namespaces. The fragments captured at Host B in 28 cases matched the app (including 1500/532/1500, 1500/1000/576, and MTU 530). The app's ping output matched real `ping` line for line in 24 cases (default, -M do, and -M dont, including the 552 floor).
- 3,600 generated questions (120 rounds per level) were re-checked independently, with no wrong keys and no duplicate choices.
- The Lesson 4 puzzle has answers (Link 3 MTU 444 to 507).
- A completion code round-trips, and an edited code is rejected.

**Smoke test** (`../testing/smoke_test.py`): all 6 lessons, a round per level, no console errors, and no sideways scrolling at 390 pixels on any screen.

**Click-through:**
- Every Try it task was answered correctly, and wrong answers give nudges.
- The Lesson 2 steps, Lesson 4 send, Lesson 5 loss and timer, and Lesson 6 pings in three modes all ran.
- Lesson 6 speed slider: three pings took 17.9 s at 0.5×, 9.3 s at 1× and 3.7 s at 3×; moving it during a run changed the pace with no errors.
- Calculator presets, DF, four links, input errors, and link parameters all work.
- The instructor check accepts a valid code.
- Every lesson works at 390 pixels.
- The app still runs when the browser blocks storage.

**Other browsers:** Firefox and WebKit could not be downloaded in the cloud workspace. The code uses only features they support, but open the app in Firefox and Safari before release.

## Requirements checklist (App Requirements.md)

1. Pass. One concept, standalone and independent: no lab, team, week, or textbook is assumed or mentioned (see the naming and independence ruling).
2. Pass. Host A, R1, R2, and Host B, with generic, editable addresses.
3. Pass. Real values, predicted ping output, and real commands.
4. Pass. Linux and Windows commands with undo (MTU back to 1500, flush the learned MTU), each with a description and a Copy button.
5. Pass. Most of the app is on re-fragmentation, offsets, reassembly and loss, and DF and path MTU.
6. Pass. Every number is checked against a real kernel, Scapy, and documentation.
7. Pass. Start screen with three mode cards and a skills list.
8. Pass. Six lessons with live figures, checked tasks, a stepper, and a progress bar.
9. Pass. The calculator accepts any size and one to four links, with the working and commands.
10. Pass. Levels, generated questions, hints, streaks, stars, badges, best scores, a review list, and a completion code.
11. Pass. A pill-shaped mode switch in a sticky top bar.
12. Pass. The topology of hosts, routers, and links in Lessons 4 and 6 and the Calculator.
13. Pass. Every figure redraws from the inputs; fragments, replies, and ICMP messages move along the links.
14. Pass. The Fig library with slab boxes, titles, and legends.
15. Pass. The shared theme and the five categorical colors.
16. Pass. Built from app-template.html.
17. Pass. Short animations and a burst on 3 stars, turned off for reduced motion.
18. Pass. One HTML file of about 127 KB, with no external resources.
19. Pass. ES5-style JavaScript, with no `?.`, `??`, or `:has()`. Firefox and Safari still need to be opened.
20. Pass. No sideways scrolling at 390 pixels on any screen, or at 360 pixels across 240 Challenge questions.
21. Pass. A safe storage wrapper (APP_ID `fragviz`) and history updates inside try/catch; tested with storage blocked.
22. Pass. `?mode=`, `?lesson=`, `?level=`, and calculator parameters.
23. Pass. Real buttons and labels, focus outlines, aria-live feedback, aria-pressed, and aria-current, with color always paired with text.
24. Pass. Acronyms are spelled out at first use.
25. Pass. No tables in student-facing text.
26. Pass. The word "pod" never appears.
27. Pass. Short second-person sentences; nudges on wrong answers and full explanations in the Challenge.
28. Pass. See Deployment Guide.md and the link parameters above.
29. Pass, except Firefox and Safari, which couldn't be run here.
30. Pass. The project keeps `fragmentation-visualizer/source/` (five parts that build `fragmentation-visualizer.html`; see Files) and this README, as item 30 allows. The built single file went to the instructor and can be added to the project by hand.
31. An independent self-check tool, not tied to graded work. If a homework asks about fragmentation, release the app after it is due or present it as a self-check.
