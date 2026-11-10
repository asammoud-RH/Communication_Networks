# Bandwidth and Latency Visualizer

A standalone app that teaches how bandwidth, distance, and queues add up to latency, how many bits fill a link (the delay × bandwidth product), and what throughput a transfer really gets. It follows `../App Requirements.md` and `../App Template Guide.md`, and was started from `../app-template.html`. The hosting steps are in `../Deployment Guide.md`. The approved plan is in `PLAN.md`. Version 2.0, November 10, 2026. Catalog entry #2.

## Files

The app is one standalone file, `bandwidth-latency-visualizer.html` (about 197 KB), built from five source parts in `source/`:

```
cat source/01-head.html source/02-core.js source/03-learn.js source/04-calc.js source/05-challenge.js > bandwidth-latency-visualizer.html
```

**Not yet in the project.** The project's knowledge store was within 69 KB of its 2 MB limit, so the built file, the five source parts, and the test scripts (`testing/logic_test (bandwidth-latency-visualizer).py`, `testing/ui_test (bandwidth-latency-visualizer).py`, `testing/amber_check.py`, `testing/screens.py` for laptop and phone screenshots, and the general `testing/figure_check.py`, which works for any app) went to the instructor as files. Upload them to this folder once there is room.

On Windows, use `copy /b source\01-head.html + source\02-core.js + source\03-learn.js + source\04-calc.js + source\05-challenge.js bandwidth-latency-visualizer.html`. To edit the app, change the part that holds the code, then rebuild and rerun the tests:
- 01: the theme and the page;
- 02: the model, the number formats, the figures, and the commands;
- 03: the lessons;
- 04: the calculator;
- 05: the Challenge, navigation, and startup.

## Version 2.0 changes (calm screens and the November 10 presentation rules)

- **Lesson map.** Learn opens on a map of the six lessons in three parts (Part 1: One packet on one link, lessons 1 to 3; Part 2: Keeping the link busy, lessons 4 and 5; Part 3: A real path, lesson 6), one line each on what the student will do. It can be reopened from the first pill in the lesson bar (`?lesson=0`).
- **Lesson bar.** One row: the map, then numbered pills; only the current lesson shows its title (on phones, numbers only).
- **Calm lesson layout.** Every lesson has the same shape:
  - left: the kicker (lesson number and part), a short intro (two short paragraphs, or a formula and three bullets in Lesson 2), **Show more** and **Terms in this lesson** (both closed), then the controls;
  - right: one main figure with its Send button, time slider, and speed menu directly above it, a key, and two or three key results; every other figure and the full numbers are in closed sections under it;
  - below the text: the Try it task and Back / Next.
  - On phones the order is intro, controls, figure, Try it.
- **What moved into closed sections.** Lesson 1: the unit converter (the time and wire figures are one card with an "In time / On the wire" switch). Lesson 2: the time diagram, and the numbers with the delay bar. Lesson 4: the full numbers. Lesson 5: the five moments, bandwidth versus throughput over time, and throughput for every bandwidth. Lesson 6: the time diagram, the delay on each link, the pings, the commands, and the queue settings.
- **Terms in every lesson.** Each lesson has a "Terms in this lesson" list, and every acronym and unit is spelled out at its first use in that lesson, including Show more (for example Mbps, µs, ns, ms, RTT, ACK, TCP, TTL, VM, KB and KiB).
- **Concepts before use.** Logarithmic axes are explained above the plots; Lesson 4's intro points to the round-trip controls (Show, Window) before they appear.
- **Controls next to every moving figure, in sync.** A figure in a closed section that moves with time (the Lesson 2 and Lesson 6 time diagrams, Lesson 5's bandwidth-versus-throughput figure, the Calculator's time diagrams) has its own Send button and time slider, tied to the main one.
- **Animations start from the beginning.** Every time slider opens at t = 0, with nothing sent ("Nothing has been sent yet. Press Send…").
- **Speed menu, 0.5× by default.** Every Send button has a speed menu (0.25×, 0.5×, 1×, 2×). The default is 0.5×, half the old pace; the choice is shared by every animation, can change mid-run, and is remembered.
- **Calculator.** A "One link / A path of links" switch shows one calculator at a time. Four key results are open; all the numbers and the working are closed. The time diagram, the delay bar, the pipe, bandwidth versus throughput, and the commands are closed sections.
- **Challenge.** The intro is two sentences; the scoring rules are under "How scoring works".

## Version 1.4 changes

- **The pipe now shows the transfer completing.** Lesson 4 has a message size (1 MB by default; leave it empty to keep sending). Under Send, the pipe fills, stays full while the message streams, and empties after the last bit leaves Host A. Host A's bar counts down "% to send" and Host B's bar counts up "% received". When the last bit arrives, Host B turns green and a "Transfer complete" banner appears. The text gives the completion time (one delay for the first bit plus the time to send it all) and the throughput. The animation stops at completion.
- **Windows are sent one per round trip.** With a window smaller than RTT × bandwidth, Host A sends a burst, waits for the acknowledgment, and sends the next. The pipe shows each burst crossing with gaps between them, and the text counts the windows.
- **Lesson 5's transfer is drawn as the same pipe.** The file's data flows from Host B to Host A through a pipe whose width is the bandwidth. Host A's bar fills, and the transfer ends with a completion banner and a green Host A.
- When the pipe is too short for the banner, the banner sits beside Host B instead.
- **Fix (November 10):** the amber first-bit column in Lesson 4 used to lag one column behind the leading edge whenever the first bit was in the first half of a column, so it jumped back and forth. Amber now always marks the column the first bit is in, the leading lit column. `../testing/amber_check.py` scrubs Lesson 4 through one-way, round-trip, and 64 KB window modes and checks that no lit column is ahead of amber. It found the lag in 32 of 66 frames of the old build and in none of the fixed build.

## Version 1.3 changes

- **Lesson 1: bits on the wire, in meters.** A new figure lays eight bits of each bandwidth along the wire against a ruler in mm, cm, m, or km, with an everyday object for scale: a grain of rice at 100 Gbps, the tallest building at 1 Mbps, and so on. "Watch the bits move" slides both rows along at the same signal speed, slowed down by a stated factor. It shows that the faster link's bits are shorter, not faster. The text explains why a bit has a length: its start is already travelling while the rest is being sent.
- **Lesson 4: a pipe that fills.** Send and a time slider fill the pipe over time.
  - The bits are a grid of blocks: more rows for more bandwidth, more columns for more delay.
  - An amber column marks the first bit, a ruler on top marks the delay, and live counters show the bits sent, on the link, and arrived.
  - A sentence says what is happening. At one delay, the pipe is full and holds delay × bandwidth.
  - In round-trip mode, an acknowledgment (ACK) comes back along a return path. With a small window, Host A runs out, the pipe empties, and the final sentence gives the throughput, window ÷ RTT.
- **Lesson 5: the transfer to completion.** A new animation with Send and a time slider:
  - The request goes from Host A to Host B, and the pipe stays empty.
  - The file then streams back at the full bandwidth. Host A's "received" bar and Host B's "still to send" bar fill and drain.
  - At the end the figure says "Transfer complete" with the throughput, next to the bandwidth.
  - Five timed moments are listed, each with a Show button.
  - The bandwidth-versus-throughput figure shows the same moment as a dashed line.
- **Phones.** On narrow screens, each lesson's controls move into a card directly above its figures, so the figure changes in view.
- **Fair tasks.** Every part of the Lesson 2 task (queue, transmit, propagation) is at least 5% of the answer. In Lesson 6, counting the transmit time only once is at least 5% off. The Medium latency question and the Hard path question follow the same rules, and the logic test checks them. An answer that leaves out a part is never accepted within the 1% tolerance.
- **Labels:** distances use mm, cm, m, or km; a pipe shorter than one bit says "less than 1 bit".

## Version 1.2 changes

- **Lesson 2: the message's journey.** A new animation shows the message travelling from Host A to Host B.
  - Host A's box empties as its bits leave ("% to send"), the bits cross the link as a train with the first bit (amber) and the last bit (navy) marked, and Host B's box fills ("% received").
  - The train's length on the link is to scale (transmit time ÷ propagation delay), with a minimum size and a note when it is drawn larger than to scale. When the transmit time is longer than the propagation delay, the train fills the link: the first bit arrives before the last bit leaves.
  - A sentence under the link says what is happening at that moment.
  - A time axis marks the four moments in time order: the first bit leaves Host A (queue), the last bit leaves (queue + transmit), the first bit reaches Host B (queue + propagation), and the last bit reaches Host B (the latency). The list beside it gives each time and turns each moment green as it happens, and a Show button jumps to that moment.
  - The journey and the space-time diagram share one Send button and time slider. The journey replaces the simpler wire figure in Lesson 2.

## Version 1.1 changes

- **Bandwidth drawn as width.** Every link in the wire figures is drawn thicker when its bandwidth is higher, so the bottleneck is the thinnest line. A packet too short to see gets an amber pointer. The pipe's width follows the bandwidth and its length the delay, both on logarithmic scales, with a note that says so. Lesson 4 warns that pipe width is not the bit width of Lesson 1.
- **Throughput as its own idea.**
  - Lesson 5 lists the four terms that get mixed up: bandwidth (the width), latency (the length plus waiting), throughput (the rate a transfer actually gets), and delay × bandwidth (the volume).
  - A new "bandwidth versus throughput" figure in Lesson 5 and the Calculator: rate over time, with nothing during the first RTT and then the full bandwidth. The dashed throughput line is the average, enclosing the same area.
  - The Easy Challenge has a terms question.
  - Lesson 6 and the path calculator say that the thinnest link limits the path's throughput.
- **Figure fixes after a full check:**
  - Labels no longer overlap the time axis or run off the left of the space-time diagram.
  - The propagation label sits beside the band, not on it.
  - The first-bit and last-bit labels never touch.
  - Arrowheads match their line's color.
  - The Lesson 5 plot covers 1 kbps.
  - The pipe says how much of it is full.
  - "Less than 1 bit" replaces fractions of a bit.
- **Phones.** The top bar fits on one row (icon, mode switch, points). Each figure that is wider than the screen says "Swipe sideways to see the whole figure".
- **Fair tasks.** The Lesson 5 task and the Hard throughput question always pick values where the throughput is at most 90% of the bandwidth, so answering with the bandwidth is never accepted.

## Goal

Students learn to:
- convert between bits and bytes, and between decimal (KB, MB) and binary (KiB, MiB) units;
- find a bit's width in time (1 ÷ bandwidth) and its length on the wire (speed ÷ bandwidth);
- split latency into queuing, transmit, and propagation delay, and say which one dominates;
- compute the delay × bandwidth product, and the window that keeps the pipe full (RTT × bandwidth);
- compute transfer time (RTT + size ÷ bandwidth) and effective throughput, and tell latency-bound from bandwidth-bound transfers;
- follow a packet across a switch and a router (store and forward), and measure RTT, link speed, and throughput with real commands.

The names are generic: Host A, Switch, Router, and Host B (192.168.20.20 in the ping output). The address and the interface name can be edited in the commands panel.

## Modes

**Learn: a lesson map and six lessons** (layout described under Version 2.0). Each lesson has live figures and a Try it task worth 20 points the first time. Every task has a hint, and wrong answers get a nudge that names the likely mistake, such as kilobits instead of kilobytes, one way instead of a round trip, or a forgotten queue.

1. **A bit is a slice of time.**
   - Eight bits drawn on one time axis at two bandwidths (2, 4, or 10 times faster).
   - The same bits along the wire in meters, with a ruler, an everyday object for scale, and a "Watch the bits move" animation (see Version 1.3).
   - Tiles for bit width, bit length on the chosen medium, and bytes per second.
   - A unit converter that shows bits, bytes, KB and KiB, and MB and MiB side by side.
   - Task: the bit width in ns at a random rate.
2. **Three parts of latency.**
   - The journey animation (see Version 1.2): bits leave Host A, cross the link with the first and last bit marked, and fill Host B, with the four moments and their times.
   - A space-time diagram: the band's height at Host A is the transmit time and its slope is the propagation delay. A Send animation and a time slider move through it.
   - A delay bar and four examples: Ethernet, 4,000 km of fiber, geostationary satellite, and a 1 MB file.
   - Task: the latency of a generated packet.
3. **Which part dominates?**
   - Latency against message size (logarithmic) for a satellite link and a ground link, with the crossover size marked.
   - Task: which link delivers a given size first. The winner always leads by at least 30%.
4. **The pipe: delay × bandwidth (core lesson).**
   - The pipe filling with bits over time (Send and a time slider), one way or for a round trip with the returning acknowledgment, with live counters, until the message has fully arrived (see Version 1.4).
   - An optional window shows the empty part of the pipe and the throughput limit, window ÷ RTT.
   - Task: the window in KB that fills a generated path.
5. **Bandwidth is not throughput.**
   - The transfer animation from request to completion, drawn as a pipe, with five timed moments (see Versions 1.3 and 1.4).
   - Effective throughput against bandwidth, with the y = x line and the size ÷ RTT ceiling.
   - Includes the 1 MB, 1 Gbps, 100 ms RTT example (108 ms, 74.07 Mbps), the "10 times the bandwidth" tile, and the latency-bound or bandwidth-bound label.
   - Task: the effective throughput.
6. **Across a switch and a router.**
   - Host A, Switch, Router, and Host B, each link with its own bandwidth, length, and medium, plus queues at the switch and router.
   - A store-and-forward space-time diagram, per-link tiles, and the bottleneck.
   - Five pings in Linux format, with random queuing (jitter).
   - The commands panel.
   - Task: the one-way latency of a 1500-byte packet across three generated links.

**Calculator:**
- **One link:**
  - Inputs: size in any unit (bits, B, KB, MB, GB, KiB, MiB, GiB), bandwidth in any unit, distance in m or km, medium, queuing delay, and an optional RTT (blank means 2 × propagation).
  - Seven presets: local Ethernet, Wi-Fi, home broadband, across a continent, under an ocean, satellite, and 1 MB over 1 Gbps with a 100 ms RTT.
  - Four key results open (one-way latency, RTT × bandwidth, transfer time, effective throughput); all thirteen numbers and the working in closed sections.
  - The animated wire figure open; the space-time diagram, the delay bar, the pipe, and the bandwidth versus throughput figure in closed sections.
- **A path of links:**
  - One to four links, each with its own bandwidth, length, medium, and queue, plus the packet size and the number of packets.
  - Per-link delay, the one-packet latency, the bottleneck, and the time and throughput for N back-to-back packets.
  - The animated wire figure and space-time diagram.
- **Commands panel** (described under Commands below).

**Challenge:**
- **Easy:** the terms (bandwidth, throughput, latency, delay × bandwidth, jitter), MB to Mb, KiB and MiB to bytes, Mbps to MB/s, which of two sizes or rates is bigger, bit width (µs or ns), propagation delay (fiber, copper, radio), transmit delay.
- **Medium:** latency with a queue, RTT from distance, bit length in meters, one-way delay × bandwidth in KB, bits on the link, which delay dominates.
- **Hard:** effective throughput, the window to fill the pipe or the throughput a window allows, a 1500-byte packet across 2 or 3 store-and-forward links with queues, N packets across two links, "10 times the bandwidth or half the RTT?", and the time saved by a faster link (the RTT cancels out).
- Number answers count as right within 1%.
- A round is ten questions or a two-minute sprint, with hints (−3), explanations, streaks, stars, best scores, and a review list.
- **Badges:** First round, Perfect 10, On fire, Unit master, Pipe filler, Bottleneck finder, Speed runner, Scholar.
- **Completion code:** `L<score>-<right>of<total>-XXXX`, checked under "For instructors" in the footer. The checksum uses the prefix BL, so codes from other apps don't pass here.

## Model

- **Latency** = Queue + Transmit + Propagation.
  - Transmit = bits ÷ bandwidth.
  - Propagation = distance ÷ signal speed: 2.0 × 10⁸ m/s in fiber, 2.3 × 10⁸ m/s in copper cable, 3.0 × 10⁸ m/s by radio.
- **Units:** bandwidth is decimal (1 Mbps = 10⁶ bit/s). Sizes are decimal unless KiB, MiB, or GiB is chosen.
- **Delay × bandwidth:** one-way delay × bandwidth is the bits on the link. RTT × bandwidth is the window that keeps the pipe full. A window W limits throughput to min(bandwidth, W ÷ RTT).
- **Transfer:** transfer time = RTT + size ÷ bandwidth, and throughput = size ÷ transfer time. The app says that TCP's handshake and slow start make real transfers slower still.
- **Paths (store and forward):** each link adds its queue (at the device that sends onto it), its transmit time, and its propagation delay. N packets sent back to back: the last arrives (N − 1) × the bottleneck transmit time after the first.
- **Ping:** RTT = 2 × the sum over the links of (transmit + propagation), plus a random queue at each device each way. The output follows Linux iputils, including the size line, `ttl=63` after one router, the time formatting, and the min/avg/max/mdev line. Host processing time is left out, and the app says so.

## Commands (checked here against a live kernel and the man pages)

Each command has a one-line description and a Copy button. The address and interface name can be edited.

- **Linux:**
  - `ping -c 5 ADDR` and `ping -c 5 -s 1400 ADDR`
  - `cat /sys/class/net/IFACE/speed`, with a note that in a virtual machine it shows the virtual adapter's speed. A veth adapter here reported 10000.
  - `ip -s link show dev IFACE`
  - Throughput: `nc -l 5000 > /dev/null` on the receiver, and `dd if=/dev/zero bs=1M count=100 | nc -N ADDR 5000` on the sender. Lubuntu's nc is OpenBSD netcat, which has -N.
  - Delay: `sudo tc qdisc add dev IFACE root netem delay 50ms`
  - Rate limit: `sudo tc qdisc add dev IFACE root tbf rate 10mbit burst 32kbit latency 400ms`
  - `tc qdisc show dev IFACE`
  - Undo: `sudo tc qdisc del dev IFACE root`
- **Windows:**
  - `ping -n 5 ADDR` and `ping -n 5 -l 1400 ADDR`
  - PowerShell: `Get-NetAdapter | Select-Object Name, Status, LinkSpeed` and `Get-NetAdapterStatistics`
  - `taskmgr`: Task Manager, Performance tab, to watch throughput.
  - The app says Windows has no built-in command to send a test stream or to add delay.
- **Still to bench-test on the shipped Lubuntu image:**
  - `netem delay`: the cloud kernel here has no netem module, so this command was not run.
  - Whether `/sys/class/net/enp0s3/speed` prints 1000 inside VirtualBox.
  - That `nc -N` and `tc` are present. Both are standard in Ubuntu: netcat-openbsd and iproute2.

## Link parameters

- `mode=learn|calc|challenge`
- `lesson=0..6` (0 is the lesson map)
- `level=E|M|H`
- Calculator:
  - `size=` and `unit=b|B|KB|MB|GB|KiB|MiB|GiB`
  - `bw=` in bits per second
  - `dist=` in meters
  - `medium=fiber|copper|radio`
  - `q=` (queuing delay) and `rtt=`, in ms

Moodle: its filters can turn "<" into "&lt;" and break a single-file app. Turn filters off for the File resource, or host the app elsewhere and link it as a URL resource.

For example: `bandwidth-latency-visualizer.html?mode=learn&lesson=4`, or `bandwidth-latency-visualizer.html?mode=calc&size=1&unit=MB&bw=1000000000&dist=10000000&medium=fiber` (1 MB over 1 Gbps with a 100 ms RTT).

## Tests (November 10, 2026)

**Logic test** (`../testing/logic_test (bandwidth-latency-visualizer).py`, independent Python with exact fractions):
- 2,000 random model cases matched: one packet over 1 to 4 links, N-packet messages, transfer, window throughput, bit width and length, and delay × bandwidth.
- `messageTime` also matched an event simulation of packets queuing at each store-and-forward link.
- 4,500 generated questions (150 rounds per level, all 23 question kinds, including the new terms question) had their answer keys re-derived with no mismatches. Choice questions have no duplicates, and their winners lead by the stated margins (at least 3 times for "which dominates", at least 20% for "faster or closer").
- The ping model with no queues matched the formula. The first two output lines match real iputils output character for character.
- A completion code round-trips, and an edited code is rejected.

**Real kernel** (network namespaces Host A, Router, Host B, with tbf rate limits):
- Through a 100 Mbit link and then a 10 Mbit bottleneck, `dd | nc -N` reported 1.2 MB/s (about 9.75 Mbps). That is the bottleneck rate less header overhead, as the app's note says.
- Real `ping -s 1472` printed `1472(1500) bytes of data` and `1480 bytes from … ttl=63 time=… ms`, the same as the app.
- The summary line format matched.
- `tc qdisc del … root` removes the limit.
- Adding a second root qdisc fails ("Exclusivity flag on"), which is why the app says only one can be in place.
- netem is not available on this kernel, so delay was not run.

**Figure check** (`../testing/figure_check.py`, new): it drives every lesson, its examples, and extreme values (1 kbps to 100 Gbps, 0 m to 71,572 km, queues, windows), every calculator preset, and paths of 1 to 4 links, at 1280 and 390 pixels. In version 1.2 it also stops Lesson 2 at nine moments of each example and at each of the four Show moments. In version 1.3 it also covers Lesson 1's bits in meters at five bandwidths and three media, Lesson 4's pipe at five moments in one-way and round-trip modes with and without a window, and Lesson 5's transfer at five moments and each Show button for four transfers. In version 2.0 it opens every closed section and both Lesson 1 views before each check, so folded figures are checked too. In version 1.4 it also runs Lesson 4 with a message (one way, round trip, 64 KB and 200 KB windows, and a 1 KB message) through to completion. It saved 1,192 figure images and found no overlapping or clipped text. The images were also looked at.

**Smoke test** (`../testing/smoke_test.py`): all 6 lessons, a round at each level, no console errors, and no sideways scrolling at 390 pixels on any screen.

**Click-through** (`../testing/ui_test (bandwidth-latency-visualizer).py`, 82 checks, all passed in three runs with fresh random tasks):
- The lesson map shows six lessons in three parts, is reachable from the lesson bar, and opens Lesson 1.
- The speed menu defaults to 0.5×, and a changed speed is kept from lesson to lesson.
- Every animated lesson opens at t = 0, and every lesson has Show more and Terms in this lesson, both closed.
- The Lesson 2 time diagram's slider moves with the main slider.
- The Calculator shows one link or a path, not both.
- On a phone, every lesson shows the controls, then the figure, then Try it, and no page scrolls sideways with every closed section opened.
- Every Try it task was solved from its text, with an independent calculation.
- A wrong answer and the hint were checked in every lesson.
- Lesson 4 with the 1 MB message ends with "Transfer complete" and 100% received; with the message cleared, the round trip ends with the acknowledgment.
- The Lesson 2 journey shows the first or last bit on the link mid-animation, the Show buttons jump to the first and the last moment (one and then four moments marked done), the satellite example gives 238.6 ms, and the time slider works.
- Lesson 1's bits move. The Lesson 4 window throughput is right (64 KB over a 100 ms RTT gives 5.12 Mbps), and the round-trip animation ends with the acknowledgment and RTT × bandwidth. Lesson 5's transfer starts with the request, its Show button jumps to the completed transfer, and Send plays to completion.
- Lesson 6's five pings and its animation ran.
- All seven calculator presets ran, the 1 MB / 1 Gbps / 100 ms case gives 108 ms and 74.07 Mbps, and binary units, bad input, the path editor, and the Windows tab all work.
- Link parameters work in both directions.
- The instructor check accepts a valid code and rejects an edited one.
- A perfect Hard round earns 3 stars, the badges, and a code.
- The app works with storage blocked and with reduced motion.
- Every lesson and a Hard question fit 390 pixels.

**Calm screens** (App Requirements, November 10 rule): every lesson, the lesson map, the Calculator, and the Challenge were looked at on a laptop (1366 × 768) and a phone (390 pixels). Each lesson shows a short intro, its controls, one figure with its controls above it, and the Try it task; everything else is closed. Before version 2.0, lessons opened with five or six paragraphs, two to four figures, and up to six stat tiles, and the Calculator had twelve tiles, the working, and four figures all open.

**Other browsers:** Firefox and WebKit could not be downloaded in the cloud workspace. The code uses only features they support (ES5-style JavaScript, plain SVG markers with `orient="auto"`), but open the app in Firefox and Safari before release. (The phone hint uses `position: sticky`, which both support.)

## Requirements checklist (App Requirements.md)

- Calm, uncrowded screens: Pass (version 2.0, see Tests).
- Name rule: Pass. "Bandwidth and Latency Visualizer", in files `bandwidth-latency-visualizer/`, with `APP_ID` `bandwidth-latency-visualizer` and test object `window.BandwidthLatencyVisualizer`. The app never mentions a book, a lab, or the course. The 1 MB / 1 Gbps / 100 ms and 50 ms × 45 Mbps cases appear as plain examples.
1. Pass. One concept, standalone.
2. Pass. Host A, Switch, Router, and Host B, with generic, editable values.
3. Pass. Real values, predicted ping output, and real commands.
4. Pass. Linux and Windows commands with descriptions and Copy buttons, and an undo for every change (`tc qdisc del`). The others only read.
5. Pass. Most of the app is on the hard parts: transmit versus propagation, the pipe and the window, latency-bound transfers, and store and forward.
6. Pass. Every number was checked against an independent implementation, an event simulation, and a real kernel where possible. netem still needs the bench test above.
7. Pass. A start screen with three mode cards and a skills list.
8. Pass. A lesson map and six lessons with live figures, checked tasks, a lesson bar, and a progress bar.
9. Pass. The one-link and path calculators accept any valid input and show the working and the commands.
10. Pass. Levels, generated questions, hints, streaks, stars, badges, best scores, a review list, a sprint, and a completion code.
11. Pass. A pill-shaped mode switch in a sticky top bar.
12. Pass. Wire figures of Host A to Host B, and Host A, Switch, Router, Host B, with ports and links labeled by bandwidth and length.
13. Pass. Every figure redraws from the inputs, and bits move along the links in sync with the space-time diagram.
14. Pass. The Fig library with slab boxes, titles, and legends wherever color carries meaning.
15. Pass. The shared theme. Band colors skip amber, which is kept for queues.
16. Pass. Built from app-template.html.
17. Pass. Short animations and a burst on 3 stars, turned off for reduced motion.
18. Pass. One HTML file of about 197 KB, with no external resources.
19. Pass. No `?.`, `??`, or `:has()`. Firefox and Safari still need to be opened.
20. Pass. No sideways scrolling at 390 pixels, and figures scroll inside their box.
21. Pass. A safe storage wrapper and history updates in try/catch, tested with storage blocked.
22. Pass. `?mode=`, `?lesson=`, `?level=`, and the calculator parameters.
23. Pass. Real buttons and labels, focus outlines, aria-live feedback, aria-pressed, aria-current, and color always paired with text.
24. Pass. Acronyms are spelled out at first use: Mbps, Gbps, KB, KiB, MB, MiB, RTT, TCP, TTL, VM, ACK (acknowledgment), µs, and ns.
25. Pass. No tables in student-facing text.
26. Pass. The word "pod" never appears.
27. Pass. Short second-person sentences, nudges on wrong answers, and full explanations in the Challenge.
28. Pass. See Deployment Guide.md and the link parameters above.
29. Pass, except Firefox and Safari, which could not be run here.
30. Partly. This README and PLAN.md are in the project. The app, its source parts, and its tests went to the instructor as files, because the project was almost full (see Files).
31. A self-check tool. The instructor has not yet said whether graded homework or exams cover delay and bandwidth. If they do, release the app after that work is due, or present it as a self-check.
