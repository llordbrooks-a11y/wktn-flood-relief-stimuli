WKTN News 5 / Riverside Relief Wishlist — Donor Pool x List Type x Stage
==========================================================================

24 standalone HTML files: 2 disaster stages (Response, Preparedness) x 3
donor-pool levels x 4 list types, per the Clearing Rules and Donation
Overshoot protocol, Study 1, as revised in conversation (v3 — no cost/no
$60 cap; select-2-units donation task with duplicates allowed; 3 opt-out
alternatives with open-ended follow-up questions; stage headline
generalized for Preparedness; pool percentages recalibrated with a
popular/non-popular item split; item detail text is black, not grey).

FILE NAMING
  wishlist_stage-<stage>_pool-<pool>_list-<list_type>.html

  <stage>:       response | preparedness
  <pool>:        none | small | large
  <list_type>:   control | static | clearing_donated | clearing_needed

STAGE HEADLINES
  response:      "Help Riverside Relief Respond to the Cumberland River Flooding"
  preparedness:  "Help Riverside Relief Prepare for Cumberland River
                  Flooding"
  Everything else (org blurb, item list, task copy) is identical across
  stages by design — only the headline differs, so a single "preparedness"
  set covers both the flood-watch and flood-warning video stimuli. The
  modifier ("potential"/"impending") was dropped from the headline rather
  than picked one way, since "impending" overstates the Watch condition's
  certainty and "potential" understates the Warning condition's — a bare
  "prepare for Cumberland River Flooding" sits under both without
  conflicting with either video's severity level.

THE DONATION TASK (updated)
  No price, no $60 cap. Each item row has a +/- quantity stepper instead of
  a single Select button, so participants choose 2 total donation units
  and CAN put both on the same item (e.g., 2 cases of bottled water) —
  duplicates are explicitly allowed, not just tolerated. The cap is on the
  running total across all items, not per item: once 2 units are picked
  anywhere, every item's "+" disables until one is removed. "Continue" is
  disabled until exactly 2 total units are selected (unless an opt-out
  alternative is chosen instead — see below).

  The item detail line (Units needed / Already donated / Still needed)
  is now solid black instead of grey, for readability.

OPT-OUT ALTERNATIVES (new)
  Below the item list, three mutually-exclusive radio options let a
  participant decline the item-selection task entirely:
    1. "I'd prefer to donate an item that is not on the Wishlist"
    2. "I'd prefer to donate a gift card or make a monetary donation instead"
    3. "I'd prefer not to make a donation at this time"

  Choosing one of these resets any item quantities back to 0 (and vice
  versa — picking an item clears a chosen alternative), so exactly one of
  "pick 2 units" or "pick one alternative" is ever in progress. Selecting
  an alternative reveals a matching open-ended "Why?" question in a
  textarea; Continue stays disabled until that textarea has text in it.
  The three follow-up prompts (parallel wording, each naming Riverside
  Relief and framed around improving the donor experience):
    - "...please share why you'd prefer to donate an item that is not on
      the Wishlist."
    - "...please share why you'd prefer to donate a gift card or make a
      monetary donation instead."
    - "...please share why you'd prefer not to make a donation at this
      time."
  (Full text is in the page; wording was tightened slightly from the
  original draft for grammar/parallelism — flag if you'd like it changed.)

POOL CALIBRATION (changed from v1)
  The aggregate pledged/needed ratio across all 12 items now matches each
  pool's stated percentage:
    none  -> 50% (assumed benchmark, halfway between small and large)
    small -> ~20% (19.95% exactly, "roughly 20%" per the on-screen sentence)
    large -> ~80% (80.05% exactly, "roughly 80%" per the on-screen sentence)

  Within every pool, four "popular" items (bottled water, baby formula,
  blankets, hygiene kits) are funded at a consistently higher rate than
  the other eight items, with no overlap between the two groups:
    none:  popular 60.0%   vs  non-popular 38.75-40.0%
    small: popular 23.3-24.0%  vs  non-popular 15.0-16.25%
    large: popular 96.0-96.7%  vs  non-popular 62.5-63.0%

  Note: under this calibration no item ever reaches exactly 0 units still
  needed (100% funded) — even the large-pool popular items top out around
  96-97%. There is no "FILLED" / struck-through state for a fully-funded
  item — that treatment was removed; clearing_needed always shows a plain
  "Still needed: <count>" number, however low.

  Units needed per item are unchanged from the original protocol's §4.5
  table and are identical across both stages, consistent with the
  protocol's statement that item identity/quantity don't vary by stage.

EACH PAGE IS SELF-CONTAINED
  No external resources, no build step, no General Relief Fund field (the
  cash option was removed along with price). Open directly or host/iframe
  anywhere for Qualtrics.

GETTING THE DATA OUT (for Qualtrics)
  On "Continue," each page:
    1. Calls `window.getWishlistSelection()` to build a payload (shape
       below) and stores it in `sessionStorage['wktn_wishlist_selection']`.
    2. If iframed, also posts it to the parent window via
       `postMessage({type: "wktn_wishlist_submit", payload}, "*")`.
    3. `window.getWishlistSelection()` is also a plain global function you
       can call directly if you paste a page's body/script into a
       Qualtrics custom-JavaScript question instead of using an iframe.

  Payload shape has two forms depending which path the participant took.
  There is now a top-level "mode" field: "items" for the item-selection
  task, or one of "not_on_list" / "monetary" / "no_donation" for an
  opt-out alternative.

  mode = "items":
  {
    "condition": {"stage": "preparedness", "pool": "large", "list_type": "clearing_needed"},
    "mode": "items",
    "selected": [
      {"id": "water", "qty": 2, "popular": true, "trueNeeded": 400,
       "truePledged": 384, "trueRemaining": 16, "truePledgedPct": 96.0}
    ],
    "totalUnits": 2,
    "popularCount": 2,
    "nonPopularCount": 0,
    "timestamp": "..."
  }
  Note "qty" on each selected item (1 or 2) — a duplicate pick (both units
  on the same item) collapses into ONE entry with qty:2 rather than two
  separate entries, and popularCount/nonPopularCount sum qty, not item
  count, so a double-pick of a popular item correctly counts as 2.

  mode = "not_on_list" | "monetary" | "no_donation":
  {
    "condition": {"stage": "preparedness", "pool": "large", "list_type": "clearing_needed"},
    "mode": "no_donation",
    "altExplanation": "<the participant's free-text answer>",
    "timestamp": "..."
  }
  There is no "selected" field in this shape — filter/branch your
  Qualtrics logic on "mode" first.

  truePledgedPct/trueRemaining (items mode only) are each selected item's
  TRUE fulfillment state regardless of what that condition displayed —
  useful for scoring whether participants gravitated toward already
  well-funded items even under Control/Static, where they couldn't see it
  directly.

VERIFIED
  - All 24 pages pass a structural check: 12 item rows (each with a +/-
    stepper) and 3 opt-out radio options, no leftover price/cost markup,
    pool sentence present/absent correctly, headline matches stage.
  - Exercised end-to-end in a headless browser across all 24 files:
    picking the same item twice collapses to one qty:2 entry and reaches
    the 2-unit cap; picking two different items (1 each) also reaches the
    cap; the cap blocks further "+" clicks anywhere once reached; "-"
    decrements correctly; choosing an opt-out alternative resets any item
    quantities to 0, requires non-empty follow-up text before Continue
    enables, and produces the correct mode/altExplanation payload;
    picking an item after choosing an alternative clears that choice and
    hides its follow-up box (and the reverse already worked before).

NOT YET DONE
  - Real Qualtrics wiring (the postMessage listener / embedded-data
    capture on the Qualtrics side, now also needs to branch on "mode").
