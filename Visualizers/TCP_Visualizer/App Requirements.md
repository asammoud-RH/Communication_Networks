# Course App Requirements

Every interactive app for this course must meet every requirement below. This list is the contract. The App Template Guide explains *how* to meet each requirement, and app-template.html already meets most of them. Before an app is released, go through this list item by item and record in the app's README that each item passes.

The instructor set these requirements in November 2026, when the first app (now Subnet Visualizer) was built. Change them only when he asks.

## Name and independence (instructor's ruling, November 2026)

- **Name every app "<Topic> Visualizer".** The first app under this rule is **Fragmentation Visualizer**. The title, top bar, and start screen use that name, and the files use its lowercase, hyphenated form: `claude/Interactive tools/<topic>-visualizer/<topic>-visualizer.html`, with `APP_ID` and the `window.<Name>` test object named to match. Do not use "Lab" or "Demonstration" in a name. (The first app, Subnet Lab, was renamed Subnet Visualizer under this rule on November 10, 2026.)
- **Every app is an independent tool.** It is not attached to the textbook or to the course labs. The app's text never mentions "the book", its figures, examples, or sections, and never mentions a lab, a lab handout, or the course. A classic textbook case may still appear as a plain example (for example, "1400 bytes over a 532-byte link"), without credit to the book. Planning notes such as Demonstration catalog.md can still record which materials an app supports.

## Calm, uncrowded screens (instructor's ruling, November 10, 2026)

- **The interface must be visually appealing and must not overwhelm students.** The instructor found the existing apps crowded and overwhelming. This rule outranks adding more content to a screen: when a screen feels busy, show less, not smaller.
- How to meet it:
  - One main idea per screen. A lesson step shows one figure and the few controls that figure needs; everything else waits for the next step.
  - Hide what is optional. Extra detail, the working behind an answer, long command lists, and advanced settings go behind a "Show more" or `<details>` toggle, closed by default.
  - Few choices at once. Show only the buttons the student needs right now; move rarely used actions to a menu or the footer.
  - Generous space. Leave clear margins between panels, keep paragraphs to two or three short sentences, and never shrink text below the template's sizes to fit more in.
  - Quiet color. Use color to point at the one thing that matters on the screen, not to decorate every element.
  - Check before release. Look at a screenshot of every lesson step and every Calculator and Challenge screen on a laptop (1366 × 768) and a phone (390 pixels wide), and ask: would a first-time student know at once where to look and what to do? Record the result in the README.

## A. Purpose and content

1. **Teach one concept, standalone.** The app covers one networking topic (for example subnets, routing, or TCP (Transmission Control Protocol) congestion control). It does not depend on a particular lab, team, bench, week, or textbook, so anyone can use it on its own.
2. **General names.** Use general terms such as Host A, Host B, Switch, Router, Client, and Server. Do not use lab-specific names such as VM A, team numbers, or the instructor's bench addresses. Default values are generic and editable (Subnet Visualizer uses 192.168.10.0/24).
3. **Practical skills are the goal.** The student leaves able to do something: compute a real value, predict what a real tool will show, or run a real command.
4. **Real commands for Linux and Windows.** Whenever the topic has commands, the app shows the Linux and Windows commands for the task, including how to undo it (for subnets: join and leave a subnet). Each command has a one-line description and a Copy button. Commands are checked against current documentation.
5. **Explore the hard part.** The app spends most of its time on the idea students find hardest, not only the basics (for subnets: overlapping subnets and hosts with different masks).
6. **Technically exact.** Every number, outcome, and message matches what real systems do. Check it against the course handouts where they cover the same case, but do not refer to them in the app.

## B. Structure: three modes

7. **Start screen** with three mode cards and a "What you will be able to do" list.
8. **Learn mode: a guided walk-through.** Four to seven short lessons, each with one idea, live figures, and a Try it task that the app checks and gives feedback on. A stepper and progress bar show progress.
9. **Calculator mode: direct answers.** For students who only want the result. It accepts any valid input, shows results with the working, and gives the commands.
10. **Challenge mode: gamification.** Easy, Medium, and Hard levels; freshly generated questions each round; hints that cost points; streak bonuses; stars; badges; best scores; a review of answers; and a completion code that the instructor can check in the app.
11. **Mode switching** is a pill-shaped switch in a sticky top bar, not plain tabs.

## C. Figures

12. **A figure of how things are connected.** When the topic involves devices, the app draws the topology (hosts, switch, router, links, ports).
13. **Figures adapt to the student.** Every figure redraws from the current inputs, so it changes as the student changes addresses, masks, or settings. Animations show packets moving along the links where that helps (for example ARP (Address Resolution Protocol) requests, replies, and pings).
14. **Course figure style.** Figures use the built-in Fig library and the slab look (boxes with heavy top and right edges), follow the project's Figure Style Guide.md, and have a title, a key when color carries meaning, and readable text.

## D. Look and feel

15. **Attractive and appealing to students**, using the shared theme in app-template.html: brand cyan #00A2D7, navy, light blue panels, amber for points and highlights, teal and coral for right and wrong, and the five categorical colors. Do not invent a new palette. Screens must also be calm and uncrowded (see "Calm, uncrowded screens" above).
16. **Same theme in every app.** Start from app-template.html, so all apps share the same top bar, cards, buttons, figures, and Challenge screens.
17. **Motion with restraint.** Short feedback animations and a celebration on a perfect round, all turned off when the system asks for reduced motion. **Every animated diagram has a speed bar** (instructor's ruling, November 10, 2026): a slider next to its play and step controls that sets the speed of all the app's diagram animations (auto-play stepping, moving markers, packets on links). One setting is shared by every speed bar in the app and remembered in the browser. It starts at half speed (0.5×), because 1× was too fast for students; TCP Visualizer offers ¼×, ½×, 1×, 2×, and 4×.

## E. Technical

18. **One standalone HTML (HyperText Markup Language) file.** Everything is inside it, with no internet access needed: no libraries, web fonts, images, or content delivery networks. It runs from a double-click, a Moodle file, a web server, or an iframe.
19. **Works in all current browsers:** Chrome, Edge, Firefox, Safari, and phone browsers. Use only widely supported JavaScript and CSS (Cascading Style Sheets): no build tools or modules, no `?.`, no `??`, and no `:has()`.
20. **Works on phones** down to 360 pixels wide, with no sideways scrolling of the page. Long commands and lists in question text must wrap.
21. **Never breaks when storage or history is blocked.** Saved progress uses the safe storage wrapper with a unique `APP_ID`, and address bar updates are inside try/catch.
22. **Link parameters** open a specific mode or lesson (at least `?mode=` and `?lesson=`), so Moodle pages and homework can link into the app.
23. **Accessible:** real buttons and labels, visible keyboard focus, live regions for feedback, and color always paired with text.

## F. Writing

24. **Spell out every acronym** the first time it appears in the app.
25. **No tables** in student-facing text; use lists and stat tiles.
26. **Never use the word "pod".**
27. **Short, simple sentences** in the second person. Wrong-answer feedback nudges without giving the answer away; Challenge explanations give the answer and the reason.

## G. Delivery and records

28. **Deployment instructions.** Each app can be shared through Moodle, GitHub Pages, or a personal website as described in Deployment Guide.md. Anything specific to the app (for example its link parameters) goes in its README.
29. **Tested before release:**
    - The logic test re-checks all math and every generated answer key against an independent implementation (see `testing/`).
    - `testing/smoke_test.py` runs clean: every lesson, a round at each level, no console errors, and no sideways scrolling on a phone.
    - The app is opened in Firefox and Safari when possible.
30. **Saved to the project** as `claude/Interactive tools/<topic>-visualizer/<topic>-visualizer.html` with a README listing the lessons, question types, model, link parameters, test results, and this checklist's outcome. If the file is too large for the project tool to upload in one piece, save its parts in `source/` with the build command in the README.
31. **Release caution.** If the app answers a graded lab or homework item, the README says so, and the app is released after that work is due or used only as a self-check.
