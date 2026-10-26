# KMK vPub Control — User Manual

Moe Azizi, Crestron Certified Programmer

## About

KMK vPub Control automates a pop-up bar and restaurant complex: six rentable Storefronts (A to F) and six kitchen Workspaces (1 to 6) behind them. Members log in, rent a Storefront, add adjoining Workspaces, buy supplies, and pay by area and time.

| Item | Detail |
| --- | --- |
| Author | Moe Azizi, Crestron Certified Programmer |
| Production program | C# for SIMPL# Pro, one program loaded on every processor |
| Target hardware | Crestron RMC4 per Storefront (also runs on VC-4), TSW-770 touchpanel at IP-ID 2A |
| Connected systems | Access card reader, vPub member database, HGVR Configurator (walls and fans), QuirkyTech POS server and digital signage |
| This demo | Runs the system's behaviour in a web browser so it can be tried without a processor |

## Screen layout

The page has four areas. The clock and building status (Open, or Closed 3–5am) sit at the top right.

| Area | What it shows | What you can do |
| --- | --- | --- |
| Touchpanel | The TSW-770 for the selected Storefront: idle screen, keypad, messages, occupancy grid, shopping | Press its buttons exactly as on the wall panel. Tabs A–F switch Storefront; a coloured dot marks an active rental |
| Building | Live floor plan: rooms coloured by occupant, partition doors, exhaust fans, signage above each Storefront, temperature, last HGVR packet | Click a Storefront to view its panel |
| Test tools | Card reader, clock, processor type, configuration files | Tap member cards, speed up or jump the clock, switch RMC4 / VC-4, disconnect the panel, serve a broken data file |
| Device log | Every message between the program and the devices, newest first | Filter by channel: SYS, HTTP, CARD, DB, TP, HGVR, POS, ERR |

## Walkthrough

A full rental takes about two minutes with the clock at ×60.

1. **Log in.** On an idle panel, tap a member card in Test tools, or press the key button and type the ID on the keypad. The program looks the ID up in the member database.
   - Valid and not expired: Access Granted. Press OK to start the rental.
   - Expired, or already renting another Storefront: Access Declined.
2. **Rental starts.** The POS server receives `rent open`, the signage above the Storefront shows the member name, and the HGVR Configurator turns the Storefront fans on. The panel moves to the occupancy screen.
3. **Add a Workspace.** The grid shows all 12 spaces. Your Storefront reads Your Space; other renters show in their own colour; free spaces are light grey; closed spaces dark grey.
   - Press to ADD: the Workspace joins your rental, the door between opens and its fan starts.
   - Press to QUEUE: another member holds it; you get it when they release it, if it still adjoins your space.
   - Press to REMOVE: the Workspace leaves your rental. Any Workspace that no longer joins your Storefront is removed with it.
4. **Shop.** Press the shopping button. Add items with +, scroll with the arrows, and use the filter button to show one vendor (All vendors clears it). The cart button shows the count; in the cart, delete items or press the register button to check out. Each item goes to the POS server and the total joins your charges.
5. **End.** Press the red stop button. Doors close, fans stop, signage clears, and the panel shows the grand total of rent and shopping before returning to Waiting for Login.

## System behaviour

**Charges.** Rent accrues once per minute: total rented area (m²) × rate (£/m²/h) ÷ 60. Adding or removing a Workspace changes the area from the next minute. Total Time In and Total Charges in the panel header update every minute.

**Schedule.**

| Time | Action |
| --- | --- |
| 03:00 | End of day: all rentals end, queues cancel, walls close, fans stop, signage clears; panels show Closed for Cleaning & Maintenance |
| 04:30 | Configuration reload from profile.csv and the web server |
| 05:00 | Building reopens; panels return to Waiting for Login |

**Configuration.** At start-up, the program reads profile.csv for the web server, HGVR and POS addresses and the rental rate. It then fetches stores.json (12 spaces: decor, area, MAC, open) and retail.json (items for sale). A file that fails to parse is logged, an error shows on the panel, and the rest of the system keeps running; the two House items are always for sale.

**Devices.**

- HGVR Configurator: a 12-byte packet carries the wall bits (bytes 7–8) and fan bits (bytes 9–10); the reply returns the temperature shown in the header.
- POS server: plain-text commands ending in CR LF, such as `rent open member 3790 spaces a`, `get item member 3790 code …` and `display +a "name"`.
- Card reader: sends the member ID as plain text to TCP port 8000.

**Processor type.** The program detects whether it runs on an appliance (RMC4) or a server (VC-4) and looks for profile.csv in the right folder. The idle screen shows the processor, firmware, IP and MAC.

## Quick test checklist

The demo opens with Storefronts C and E occupied, Storefront F and Workspace 6 closed, and panel A waiting for login.

| Member ID | Result |
| --- | --- |
| 3790 | Access Granted (The Moon and Horse) |
| 1021 | Access Granted (The Crown and Firkin) |
| 777 | Access Granted (The Fox and Fiddle) |
| 001, 011, 111, 222, 333 | Access Granted (keypad demo IDs; leading zeros are fine) |
| 18780 | Access Declined: membership expired |
| 24235 | Access Declined: membership expired |
| 4021 | Access Declined: already renting Storefront C |

- [ ] Log in on panel A with 3790 and confirm Access Granted
- [ ] Add Workspace 1, then 2; watch doors D1 and D2 open and fans F2 and F5 start
- [ ] Remove Workspace 1 and confirm Workspace 2 is removed too
- [ ] Re-add Workspaces 1 and 2 on A; then on panel B, log in with 1021 and queue Workspace 2; end A's rental and confirm B receives it
- [ ] Add three shopping items, filter by one vendor, delete one item from the cart, check out
- [ ] Turn on the broken retail.json and confirm the error message and House-only list
- [ ] Switch RMC4 / VC-4 and check the platform and file path in the log
- [ ] Untick Panel connected and confirm Connecting to system – please wait
- [ ] Jump to 2:59:50 at ×60 and confirm the end-of-day close, then jump to 4:59:50 to reopen
