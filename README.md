# KMK vPub Control — live demo

**[Open the demo](https://moeslab.github.io/kmk-vpub-demo/)**

A Crestron control system for Kensington Modular Kitchens, a pop-up restaurant complex with six rentable Storefronts and six kitchen Workspaces. The demo runs in any browser, no processor needed:

- **TSW-770 touchpanel**: member login by card or keypad, occupancy grid, workspace add / queue / remove, shopping cart and checkout.
- **Live floor plan**: partition doors open and exhaust fans spin as spaces are rented; signage shows each member's name.
- **Device log**: every HGVR packet (hex), POS command, card read and configuration fetch.
- **Test tools**: speed up the clock, jump to end of day, switch RMC4 / VC-4, disconnect the panel, serve a broken data file.

See [MANUAL.md](MANUAL.md) for a walkthrough and test checklist.

The production program is C# for SIMPL# Pro on Crestron RMC4 / VC-4 with VT Pro-e touchpanels.

Moe Azizi, Crestron Certified Programmer
