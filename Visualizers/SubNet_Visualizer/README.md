# Subnet Visualizer

A standalone app that teaches the practical skills of subnetting. It meets `../App Requirements.md` (checklist below), follows the course app template (`../App Template Guide.md`), and the hosting steps are in `../Deployment Guide.md`.

Version 3, November 10, 2026. It replaces Subnet Lab (version 2, November 8), which was renamed and reworked to the later rules: the "<Topic> Visualizer" name, no references to labs or the book, calm screens, a lesson map, terms lists, and play controls next to every animated figure.

**Where the app is.** The project is at its 2 MB limit, so the app file (`subnet-visualizer.html`, about 125 KB) is not stored here; the instructor has it as a file. Only this README and the two test scripts in `../testing/` are in the project. To change the app in a new session, attach `subnet-visualizer.html`.

## Goal

Students learn to:
- read a prefix length and its mask;
- find the network address, broadcast address, host range, and host count;
- tell whether two addresses are in the same subnet;
- predict a ping when two hosts use different masks (overlapping subnets);
- put one host in two subnets;
- join and leave a subnet with Linux and Windows commands.

The names are generic (Host A, Host B, Switch). The network is 192.168.10.0/24 by default, and students can change it under "Network settings" in the footer.

## Modes

**Learn.** A lesson map opens first: six lessons in three parts, one line each on what the student will do. It can be reopened from the first pill in the lesson bar. Every lesson has the same calm layout:
- left: a short intro (two short paragraphs), the lesson's controls, then "Show more" and "Terms in this lesson", both closed;
- right: one main figure, with any play or step controls directly above it; extra figures are folded closed;
- below the intro: the Try it task (20 points the first time) and Back / Next;
- on phones: intro and controls, then the figure, then Try it.

The lessons:
- **Part 1: Read an address**
  1. **Bits and the mask.** A prefix slider from /8 to /30 and the binary figure. Task: set the slider to match a given mask.
  2. **Network address, broadcast address, and hosts.** "Show the steps" (with Reset) sets the host bits to 0 and then to 1, starting from the address itself. The address line is folded. Task: find the network or broadcast address of a random address.
  3. **Same subnet or not?** Differing network bits are outlined in red; the address line is folded. Task: yes or no for a random pair.
- **Part 2: Hosts on one switch**
  4. **Overlapping subnets (the core lesson).** Host A .40/24, Host B .200/25, Switch .254/24. The figure card has the ping controls (From, To, Ping, Step, Reset, Speed), the topology (hosts on switch ports 1 and 3, relationship line under the hosts, "reaches the switch" marks), and the ping log. Folded: how the sending host decides (outlined in step with the ping), where each subnet sits, and the neighbor test with the routing tables. Task: change only Host B's address (keep /25) so the ping works both ways; the feedback then points out that Host B lost the switch.
  5. **One host, two subnets.** Host B .150/26 (.128 to .191), which leaves out the switch at .254. Task: give Host A a second address so that A and B ping each other while B still cannot reach the switch (for example .140/26).
- **Part 3: Do it on a real computer**
  6. **Join and leave a subnet.** The commands panel shows join and leave first; the other commands and notes are under "More commands". Task: a generated command question.

The topology figure crops to the hosts in use and scales its text to the screen width (bigger on phones). Host C is supported by the code but not used in the lessons, to keep the screens calm.

**Incomplete addresses (version 3.1, November 10, 2026).** A figure never shows results for an address that is no longer in its box. While an address box is empty, half-typed, or out of range (for example 600), the box gets a red outline, the figure card fades, and a note says "Type a last number from 0 to 255. The figure updates when the address is complete." In lessons 4 and 5 the old ping log and the Try it feedback are cleared and Ping and Step are turned off. Leaving the box (Tab or a click elsewhere) puts the last good address back. Lessons 2, 3, and 6 and the Calculator's address and overlap boxes work the same way. (Before this, an empty box left the previous address's figure and ping result, such as "Destination Host Unreachable", on screen.)

**Calculator.** The main card is open: any address with a prefix length (/0 to /32) or a dotted mask gives the subnet, mask, network address, broadcast address, first and last host, host count, block size, and the binary figure. Three more cards start closed: the overlap checker (with a figure), the block split (first 64 blocks), and the commands for the address.

**Challenge.**
- Easy (/25 and /26): network and broadcast addresses, host counts, masks, same subnet.
- Medium (/26 to /28): first and last host, valid host address, commands.
- Hard (/27 to /30 and mixed masks): predicting the ping, overlap relationships.
- Ten questions or a two-minute sprint; hints cost 3 points; streak bonus; stars; best score per level; review of answers.
- Badges: First round, Perfect 10, On fire (a streak of 5), Mask master, Overlap detective, Speed runner, Scholar (all six lessons).
- Completion code `<level><score>-<right>of<total>-XXXX` (level E, M, H, or S for sprint), checked under "For instructors" in the footer. It deters casual editing but is not secure.

## Commands (checked against Microsoft documentation and the Linux `ip` command)

- **Linux:** join `sudo ip addr add ADDR/P dev eth0`, leave `sudo ip addr del ADDR/P dev eth0`. More commands: `sudo arping -D -c 3 -I eth0 ADDR`, `ip -4 addr show dev eth0`, `ip route`, `ping -c 3`, `ip neigh show dev eth0`. Changes last until the next restart; the note gives the `nmcli connection modify ... +ipv4.addresses` form (and `-ipv4.addresses` to remove).
- **Windows (as administrator):** join `netsh interface ipv4 add address "Ethernet" ADDR MASK`, leave `netsh interface ipv4 delete address "Ethernet" ADDR`. More commands: PowerShell `New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress ADDR -PrefixLength P` and `Remove-NetIPAddress ... -Confirm:$false`, `ipconfig /all`, `route print -4`, `arp -a`, `ping`. The note says the settings stay after a restart, that netsh adds a second address to an adapter that already has a static address, and that New-NetIPAddress turns off DHCP (Dynamic Host Configuration Protocol) on that adapter.

## Ping model

The network has no router, and hosts have no default route.
- **Network is unreachable:** the destination isn't in any of the sender's subnets, so nothing is sent.
- **Destination Host Unreachable:** the address is in the sender's subnet, so the sender broadcasts an ARP (Address Resolution Protocol) request. Either nobody owns the address, or the owner finds the sender's address outside its own subnets and doesn't answer.
- **3 replies:** both hosts find each other inside their own subnets.
- **Source address:** when a host has two addresses, the most specific matching subnet (the longest prefix) picks the source address.

## Technical notes

- One offline HTML file; `APP_ID`-style storage prefix `subnetviz.`; test object `window.SubnetVisualizer`; completion-code salt `SV`.
- Link parameters: `mode=learn|calc|challenge`, `lesson=0..6` (0 is the lesson map), `ip=192.168.10.130/25` (calculator), `level=E|M|H`, `net=192.168.20` (the network). Example: `subnet-visualizer.html?mode=learn&lesson=4`.
- Moodle: its filters can turn "<" into "&lt;" and break the app. Turn filters off for the File resource, or host the app elsewhere and link it as a URL resource.

## Tests (November 10, 2026, headless Chrome)

Scripts in `../testing/`: `logic_test_example (subnet-visualizer).py`, `ui_test_example (subnet-visualizer).py`, and `smoke_test.py`.
- **IP math:** 1,320 random cases across every prefix from /0 to /32 matched Python's `ipaddress`.
- **Ping model:** one-way overlap, both ways, no owner, and two-address cases give the expected outcomes.
- **Challenge:** 3,600 generated questions (120 rounds per level) re-checked independently: no wrong answer keys, no duplicate choices. Codes round-trip; edited codes are rejected.
- **Click-through:** the lesson map, all six lesson tasks solved the way a student would, the ping animation, the Calculator, and a full Hard round, with no console errors.
- **Smoke test:** map plus six lessons, a round at each level, four screens at phone width, no errors.
- **Incomplete addresses:** emptying, typing out of range, and leaving each address box in lessons 2 to 6 and the Calculator: the figure fades with the note, the old ping result and feedback are gone, Ping is off, and leaving the box restores the last good address.
- **Layout and figures:** at 1366, 1000, 768, 390, and 360 pixels, with every folded section opened, no page scrolls sideways and every address, "/", and prefix stays on one line. The topology figure was checked in 20 layouts (both lessons, before and after solving, five widths): no text overlaps and nothing falls outside the figure.
- **Calm screens:** every lesson, the Calculator, and the Challenge were looked at on a laptop (1366 × 768) and a phone (390 pixels). Each lesson shows a short intro, its controls, one figure, and the Try it task; everything else is closed.
- **Other browsers:** not yet opened in Firefox and Safari (they cannot be installed in the cloud workspace). The code uses only features they support.

## Requirements checklist (App Requirements.md)

- **Name and independence: pass.** Named Subnet Visualizer; no mention of the book, a lab, a handout, or the course in the app. The lesson scenarios use their own numbers.
- **Calm, uncrowded screens: pass.** See "Calm screens" above.
- **A. Purpose and content (1 to 6): pass.** One concept, general names, practical skills, Linux and Windows commands to join and leave a subnet, most time on overlapping subnets, outcomes that match real Linux behavior.
- **B. Three modes (7 to 11): pass.** Start screen, lesson map and six lessons with checked tasks, Calculator, Challenge, pill mode switch.
- **C. Figures (12 to 14): pass.** Topology of the hosts and the switch; every figure redraws from the inputs; animated ARP and ping with controls next to the figure; slab style with titles and keys.
- **D. Look and feel (15 to 17): pass.** Shared theme, reduced motion respected.
- **E. Technical (18 to 23): pass, with one open item.** One offline file, phone widths checked, safe storage and history, link parameters, accessibility. Item 19 still needs a check in Firefox and Safari.
- **F. Writing (24 to 27): pass.** Acronyms spelled out at first use in each lesson (with a terms list), no tables, no "pod", short sentences.
- **G. Delivery and records (28 to 31): pass, with notes.** Item 30: the app file is not in the project because the project is full (see "Where the app is"). Item 31: if graded work covers subnet math or overlapping subnets, release the app after it is due or use it as a self-check.
