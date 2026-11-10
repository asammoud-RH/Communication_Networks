# TCP Visualizer

A standalone app that teaches how TCP (Transmission Control Protocol) turns a packet service that loses, delays, and reorders packets into a reliable byte stream: the header, the handshake, sequence and acknowledgment numbers, the sliding window and flow control, loss and the retransmission timer, and closing. It is an independent tool, not attached to the textbook or the course labs. Catalog entries #25, #26, and #27; congestion control (#30) stays a separate tool. Built from `../app-template.html` and checked against `../App Requirements.md`. Version 2.3, November 10, 2026.

## Files

`tcp-visualizer.html` (about 220 KB) is built from six source parts. The project was too full to hold them, so the app, its parts, and its tests were delivered to the instructor as files. To rebuild:

```
cat source/01-head.html source/02-sim.js source/03-core.js source/04-learn.js source/05-calc.js source/06-challenge.js > tcp-visualizer.html
```

01 holds the theme and page, 02 the TCP model and the Wireshark labeler (no page code, so tests can load it alone), 03 the figures, player, capture list, and commands, 04 the lessons, 05 the Calculator, 06 the Challenge, navigation, and links. Tests: `logic_test (tcp-visualizer).py`, `ui_test (tcp-visualizer).py`, `figcheck (tcp-visualizer).py`, `terms (tcp-visualizer).py`, and the kernel captures (`*.pcap`) they use.

## Modes

**Learn: eight lessons**, each with a timeline you can step through or play, a Try it task (20 points the first time), and a "See it on your computer" box with Linux and Windows commands.
1. **A segment and its header**: pick any segment of a short connection and see its header field by field (relative or raw numbers) and the frame size Wireshark shows. Task: decode a generated header in hexadecimal (header length, flags, data length).
2. **Opening a connection**: editable ISNs (initial sequence numbers), raw or relative numbers, state diagram, and six cases: normal, port closed (RST), SYN lost, SYN-ACK lost, no answer (SYN resent after 1, 2, 4 … s, giving up at 127 s), simultaneous open. Task: the five raw Seq and Ack numbers, with wraparound.
3. **Numbering every byte (core)**: A writes, B replies; piggybacked ACKs; delayed ACKs on or off. Task: three hidden Seq or Ack values in a generated timeline.
4. **Flow control (core)**: buffer size and a reader that pauses; byte ribbons with SND.UNA, SND.NXT, RCV.NXT, the advertised and usable windows; zero window, Linux-style probe, window update; a Window Scale calculator. Task: advertised window, usable window, may A send.
5. **Loss and retransmission**: choose lost segments (or B's first ACK), SACK on or off; duplicate ACKs, fast retransmit, timeout, partial ACKs. Task: the duplicate ACK number, how many, and fast retransmit or timeout.
6. **Setting the timer**: RFC 6298 on editable samples with a plot, Karn's rule per sample, minimum RTO (none, 200 ms, 1 s), backoff. Task: new RTTVAR, SRTT, and RTO.
7. **Closing and TIME_WAIT**: A first, B first, three-segment close, simultaneous close (CLOSING), last ACK lost (FIN resent, ACK resent from TIME_WAIT), close with unread data (RST). Task: both sides' states.
8. **The state diagram, step by step**: the full state-transition diagram drawn first, with the timeline beside it; the student chooses how the connection opens and closes, or clicks an arrow (see version 2.3). Each step jumps to the next state change; the A and B markers slide along the arc that fired, the arc turns amber, and the path each host has taken stays drawn (cyan for A, violet for B). A caption names the event and the action, for example "B receives SYN and sends SYN + ACK: LISTEN → SYN_RCVD". Cases: A closes first, B closes first, three-segment close, simultaneous close, simultaneous open, last ACK lost. Task: pick Host A's or Host B's state sequence from setup to the end.

**Calculator:** a Connection Sandbox (sizes, MSS, buffers, reader, delay, link rate, Timestamps, SACK, Window Scale, delayed ACKs, Nagle, minimum RTO, who closes, half-close, listening or not, ISNs, raw numbers, lost segments by number or by clicking the timeline), with 12 presets, stat tiles, state badges in `ss` or `netstat` spelling, the timeline, buffers, state diagram, capture, and event list. Quick calculators: header decoder (hex), Seq/Ack with wraparound, windows and throughput limit, RTO, MSS from MTU. All commands.

**Challenge:** Easy (flags, next ACK, header length, MSS, state after an event, `ss`/`netstat` spellings, Wireshark labels), Medium (handshake numbers, ladders, advertised and usable windows, Window Scale, who ends in TIME_WAIT, refused/timed out/reset from a capture, payload length), Hard (Ack after a loss, fast retransmit or timeout, RTO from samples with Karn's rule, SYN give-up times on Linux and Windows, wraparound, window ÷ RTT, the impossible line in a capture, zero window). Sprint mode, hints, streaks, stars, best scores, review, 11 badges, completion code with prefix TV.

## Version 2.3 changes (November 10)

- **Lesson 5: "The gap, step by step"** replaces the buffers panel. Two rows of segment boxes, what Host A sent and what Host B has, colored acknowledged or in order, waiting, held out of order, missing, and ↻ resent. An arrow marks B's Ack (the next byte it needs), a violet bracket marks the SACK blocks, and one sentence under the figure says what is happening at that moment (the gap, how many duplicate ACKs, whether the third one has reached A, which kind of resend). Tiles: B's Ack, duplicate ACKs in a row, segments resent. Checked against every ACK in 400 random loss patterns.
- **No shifting while stepping.** The time label next to the play buttons used to wrap onto a second line as it grew, pushing every panel down and back. It now has its own line, and step sentences sit below their figures with a fixed minimum height. On a laptop nothing moves; on a phone only panels below the active figure can move.
- **Lesson 8: choose the path.** Two rows of choices replace the six cases. How it opens: B listens and A connects; both connect at once; both listen and B sends first; B listens, then quits; A gives up before an answer. How it closes: A first; B first; B closes as soon as A's FIN arrives; both at once; A closes during the handshake (with "both connect at once"). A box loses the last ACK. Clicking any arrow in the diagram picks a path that takes it and plays that step. A counter shows how many of the 20 arrows the student has taken.
- **Model additions for the rare arrows** (RFC 9293 behavior, matching Linux where Linux allows it): closing in SYN_SENT forgets the connection without a segment; closing in SYN_RCVD sends FIN at once (FIN_WAIT_1); a listening side that sends first turns active (LISTEN → SYN_SENT, which the sockets interface cannot ask for, as the lesson says); a segment that acknowledges our FIN and carries the other FIN goes FIN_WAIT_1 → TIME_WAIT in one step (before, FIN_WAIT_2 was logged for zero seconds). Host A can also listen or stay closed. The logic test runs every path through tshark and checks that all 20 arrows are taken.

## Version 2.2 changes (instructor's review, November 10)

- **Lesson map.** A first-time student opening Learn sees a map before Lesson 1: the eight lessons in four parts (Opening a connection, Moving data, When segments are lost, Closing and the whole picture), each with one line on what the student will do, done marks, and "Start lesson 1" or "Continue with lesson N". The ☰ button before the lesson numbers opens it again. Link: `?mode=learn&lesson=map`.
- **States introduced before the diagram.** Lesson 2 now says what a state is before the badges and the diagram use it. Wherever the diagram appears folded (lessons 2 and 7, the Calculator), it opens with two sentences on how to read it and points to Lesson 8.
- **Controls next to every figure.** The timeline, the state diagram, and the buffers each have their own play/step bar, all moving together, so a student never has to scroll to the other figure to step.
- **Seq numbers in lessons 5 and 7 checked.** They are correct: A's data segments go Seq=1, 101, 201, …, while B sends no data, so every B segment shows Seq=1 and every A segment shows Ack=1 (B's FIN then makes A's last Ack 2). Because this looks like a mistake, a note under the timeline now says so whenever one side sends no data.
- **Every term defined where students meet it.** Each lesson spells out SYN, ACK, FIN, RST, PSH, Seq, Len, ISN, MSS, SACK, RTT, RTO, SRTT, RTTVAR, RFC, IP, MTU, SND.UNA, SND.NXT, and RCV.NXT at first use in its own text, including the folded sections, the command notes, the Calculator labels, and the header decoder. Each lesson and the Calculator end with a generated "Terms in this lesson" list. RACK appears only in "About this app", spelled out. `test/terms.py` checks every lesson, the map, and the Calculator (0 problems).
- **Lesson 8 starts before the connection,** with both hosts in CLOSED and a caption saying who is about to listen and who is about to connect (or that both connect, in "Both open at once"). The next step moves both markers out of CLOSED.

## Calm screens (version 2.1)

Changed to meet the "Calm, uncrowded screens" rule, with students' first visit in mind:

- **Lesson bar:** one row of numbered circles. Only the current lesson shows its title, and on phones only the numbers show. Every circle has the full title as its label.
- **Short lesson text:** each lesson opens with one or two short paragraphs and the controls. Longer explanations sit behind closed "Show more" links with plain titles (for example "What each field means", "Zero window, probes, and Window Scale").
- **One main figure:** each lesson shows its timeline (Lesson 4 also shows the buffers, its core idea). The capture, the "What happened, in words" list, and the state diagram in lessons 2 and 7 are closed folders below it. In Lesson 6 the working and the backoff list are closed folders.
- **"See it on your computer"** is closed by default.
- **Lesson 8** starts at the first step instead of the end, so students step forward through the connection.
- **Phones:** the figures come right after the lesson's text and controls, and the Try it task, the commands, and Back/Next move below them. The state diagram shrinks to fit the screen, and the step text above it names each transition. Wide timelines say "Swipe sideways to see the whole figure".
- **Calculator:** shows the preset, lost segments, four number tiles, and the timeline. All settings, more numbers, the buffers, the state diagram, the capture, the event list, each quick calculator, and the commands are closed folders. The page went from about 8,000 to about 2,200 pixels tall on a laptop.
- **Start screen and footer:** shorter text; "About this app" is a closed folder.
- **Check:** every lesson, the Calculator, and the Challenge were looked at on a laptop (1366 × 768) and a phone (390 pixels wide). Each screen opens with one figure and the few controls it needs.

## Figures

- **Timeline:** every segment is drawn with the same slope, so arrows sent while others are in flight run parallel. The vertical layout is solved as a longest path over spacing rules (labels on the same side never collide; a reply leaves after the segment that caused it arrives; long silences are cut with a break mark).
- **State-transition diagram:** the classic layout, box for box and curve for curve, with event/action labels on each arc; events that are calls by the program (Passive open, Active open, Send, Close) and the timeout are in italics, packets upright, and the ACK + FIN/ACK label runs along its diagonal (Passive open, Active open/SYN, SYN/SYN + ACK, Close/FIN, FIN/ACK, ACK + FIN/ACK, Timeout after two segment lifetimes, and the rest). RST to CLOSED from any state is noted, not drawn. The markers animate over about 1 second and stand still when the system asks for reduced motion. It appears in lessons 2, 7, and 8 and in the Calculator.
- **State names** follow the diagram (SYN_RCVD, FIN_WAIT_1, TIME_WAIT …). State badges and Challenge questions also give the `ss` (Linux), `netstat` (Windows), and RFC spellings.

## Model

- Sequence numbers relative to each ISN; SYN and FIN use one number; Ack is cumulative; raw numbers mod 2^32.
- Options and sizes as Linux sends them: SYN header 40 bytes with MSS, SACK_PERM, Timestamps, Window Scale; 32-byte headers with Timestamps (1448 data bytes per Ethernet segment); SACK blocks add 4 + 8n bytes; a closed-port RST is 20 bytes.
- Receiver: advertised window = free buffer, never shrunk (rounded up to the scale step, as Linux does); out-of-order data held and SACKed; delayed ACKs (every second segment or 40 ms) when on; window update when the application frees enough space.
- Sender: limited by the advertised window only (no congestion window); sender silly-window avoidance; optional Nagle; persist timer with Linux-style probes (Seq = SND.UNA − 1, no data) doubling each time.
- Loss: RFC 6298 timer (initial 1 s, minimum 1 s or 200 ms, doubling, Karn's rule); fast retransmit on the third duplicate ACK; with SACK, the other holes are resent during recovery; partial ACKs resend the next hole. Linux's tail loss probe and RACK are named in the footer, not modeled.
- Closing: every RFC 9293 state, TIME_WAIT 60 s, RST for unread data and for closed ports. SYN retries 6 (127 s).
- Capture at Host A with Wireshark's own analysis rules (ported from Wireshark 4.2 `packet-tcp.c`): Retransmission, Fast and Spurious Retransmission, Out-Of-Order, Dup ACK, Window Full, Window Update, ZeroWindow, Keep-Alive, Previous segment not captured, in Wireshark's order.

## Link parameters

`mode=learn|calc|challenge`, `lesson=1..8` or `lesson=map`, `level=E|M|H`, `os=linux|windows`; Calculator: `preset`, `mss`, `rbuf`, `sack`, `dack`, `ws`, `close=a|b|both|none`, `lose=5-9`, `raw=1`. Example: `tcp-visualizer.html?mode=calc&preset=fast&lose=5-9&raw=1`.

**Moodle note (November 10):** the file worked on its own but broke once uploaded as a Moodle File resource: Firefox reported Quirks Mode and "missing ) after for-loop control", because every `<` in the script had become `&lt;`. Moodle's text filters can run over uploaded HTML files. Fix: turn the filters off for that resource, ask the Moodle administrator to set "Filter uploaded files" to None, or host the file elsewhere and add it as a URL resource.

## Tests (November 10, 2026)

- **Logic test:** 600 random connections in this round (1,000 in version 1) checked by independent code (every byte delivered once and in order, every ACK equal to an independent receiver's RCV.NXT, the sender inside the advertised window, contiguous sequence numbers, every loss repaired, fast retransmit only after three duplicate ACKs, timeouts no sooner than the minimum, legal state changes). 400 of them and all 12 presets were rebuilt as pcaps with Scapy and read by tshark 4.2: every Info line and label matches. 4,500 Challenge questions re-checked; the RTO estimator against exact fractions (300 series); the header decoder against 300 Scapy headers. All pass.
- **Real kernel:** Linux 6.18 network namespaces joined by a user-space lossy link. Matched: SYN and SYN-ACK options and 74-byte frames, 66-byte data and ACK frames, the four-segment close, the closed-port `[RST, ACK] Seq=1 Ack=1 Win=0` (54 bytes), RST for unread data, Window Full → ZeroWindow → Keep-Alive probe at SND.UNA − 1 → Window Update, `[TCP Dup ACK n#1]` with SLE/SRE, Fast Retransmission, and SYN resent after 1 s. Seen but not modeled: Linux's tail loss probe and RACK resend sooner; the kernel's quick-ACK start; kernel 6.18 retries the first SYNs every second (`tcp_syn_linear_timeouts`), while Ubuntu 24.04 kernels double from the start.
- **Smoke test** and **click-through test** (every Try it solved and a wrong answer rejected, every case and preset, links, the instructor code check, storage blocked): no console errors. **Figure check:** no overlapping or clipped text in any figure in every lesson, case, and preset, checked with the folders closed and again with every folder open. No sideways scrolling of the page at 390 and 360 pixels on any screen. The figure check also steps through every Lesson 8 case, and the timelines were inspected for parallel arrows.
- Firefox and Safari could not be run in the cloud workspace; open the app in both before release.

## Requirements checklist

1–6 Pass (one concept; generic Host A and Host B; real numbers, captures, and commands with undo for `tc`; most time on numbering, windows, and loss; checked against a kernel and tshark). 7–11 Pass (start screen, Calculator, Challenge, pill switch). Requirement 8 asks for 4–7 lessons; this tool has eight because the instructor asked for the state-diagram lesson on November 10. 12–14 Pass (topology as a timeline, state diagram, buffers, header, RTO plot; all redraw from inputs and animate; Fig library and slab look). 15–17 Pass. Calm, uncrowded screens: Pass (see above). 18 Pass (one file, no external resources). 19 Pass in Chrome; ES5-style code; Firefox and Safari still to be opened. 20–23 Pass (APP_ID `tcp-visualizer`). 24–27 Pass (acronyms spelled out; no tables; no "pod"; short sentences). 28 See Deployment Guide.md. 29 Pass except Firefox and Safari. 30 The project keeps this README; the app, parts, and tests went to the instructor as files because the project is full. 31 Release as a self-check until it is known which graded work asks for handshake and close numbers.

## Decisions (instructor, November 9, 2026)

Scope #25–#27 in one tool, congestion control separate; the plan's proposed defaults were approved ("go"): name TCP Visualizer; duplicate ACKs, fast retransmit, and SACK as loss detection only; delayed ACKs modeled, Nagle as a switch; self-check release; files delivered outside the project.

November 10: arrows must be parallel and aligned; add a lesson that walks the state diagram from setup to close with transitions moving as the student steps; the diagram should follow the classic layout. Done in version 2; the curves, italics, and label places were then matched to the reference figure the instructor sent.
