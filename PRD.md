# Special Occasions — Birthday Weekend Itinerary Planner

## 1. Overview

A single-page, client-side web app that turns a high-octane Boston birthday weekend (Jun 26–29, 2026) into a live, editable, shareable itinerary. The user (girlfriend planning her boyfriend's 29th) and the boyfriend each open the same URL on their own device to view the plan, track where they are in real time, see cost estimates, and independently pick which place they want to stay.

Deployed as a static `index.html` at `https://special-occasions.vercel.app/`.

## 2. Goals

- Recreate the "Andretti's" (Orlando) vibe — racing/arcade/food under one roof — in the Northeast, layered with the user's three bucket-list activities: gun range, exotic car rental, jet skis.
- Give both people a single source of truth they can consult and edit throughout the weekend.
- Surface cost transparency so the couple can decide tradeoffs (especially the supercar, which dominates the budget).
- Let each partner express a preference on lodging without needing to agree first.

## 3. Non-Goals

- No backend, no accounts, no shared sync across devices. State is local to each browser.
- No real-time inventory or booking — links open vendor sites; the app doesn't transact.
- Not a general-purpose trip planner. Hard-coded around this specific weekend.

## 4. Users

- **Primary:** the planner (girlfriend) — does most of the editing, owns the budget.
- **Secondary:** the celebrant (boyfriend) — opens the link, browses, picks his preferred stay.
- Both are non-technical end users on phones during the weekend.

## 5. Core Experience

### 5.1 Itinerary tab

- Four day cards (Fri 26 → Mon 29), each with a date chip and expand/collapse.
- Each activity row: time, name, short description, cost pill ("$X–Y for 2"), and Website / Map / Call buttons. `tel:` links open the phone dialer.
- **Live tracking:** a "Right now" bar at the top and a green "● now" tag on the active activity. The current day gets a "we are here" badge. Before Jun 26 the app runs in PREVIEW mode (mapping current time onto Saturday so the tracking is visible); on Jun 26–29 it switches to the real clock automatically.
- **Jump to now** button auto-expands and scrolls to the current activity.
- **Edit mode** per day: change times, names, notes, costs; add or delete activities. Saved to `localStorage` on each change.
- **Reset to original** restores the seed plan.

### 5.2 Where to Stay tab

- Four curated hotels (The Whitney, The Eliot, The Godfrey, XV Beacon) and four Airbnb neighborhood cards (Seaport, South Boston, North End, Back Bay).
- Each card shows: photo thumbnail (real image with SVG fallback on load error), star rating, price range, nightly + 3-night estimates, and Book/Compare/Map/Call (or Browse Airbnbs) buttons.
- Airbnb cards link to live Airbnb searches pre-filtered with the dates and `price_max=800`.
- **Per-person selection:** "Your pick" and "His pick" buttons on every card. Tap to claim, tap again to clear. Selected cards get a colored edge. Picks persist in `localStorage`.

### 5.3 Modular budget (on Itinerary tab)

- Live calculator below the day cards.
- **Supercar segment toggle:** Skip / 1 day / 3 days. Drives 60–80% of the spend, so it's the primary lever.
- **Activity checkboxes:** uncheck to exclude from the total.
- **Accommodation picks summary:** shows "Your pick" and "His pick" side by side and flags whether they match. The grand total folds in the user's pick (falls back to his if only he selected).
- Updates live on any change to selections, checkboxes, or per-activity cost edits.

## 6. Seed Data

| Activity | Source vendor | Est. cost (2 pax) |
|---|---|---|
| Boston Exotic Rentals (supercar, Fri pickup → Sun return) | bostonexoticrentals.com | $1,000–2,000+/day |
| Supercharged Entertainment (Wrentham — Sat centerpiece, ~5 hrs) | superchargedma.com | $140–250 |
| MBTA Providence Line (Mansfield → Providence, Sat night) | mbta.com | $10–25 |
| Providence nightlife (Federal Hill / The Avery / The Eddy) | goprovidence.com | $150–350 |
| Boston Firearms Training Center (Everett) | bostonfirearms.com | $150–280 |
| Boston Harbor Jetskis (Braintree) | bostonjetskis.com | $300–550 |
| Activate Games | playactivate.com | $70–90 |
| Food / drinks / gas / parking buffer | — | $300–500 |

Rough weekend totals (excl. hotel): **$2,000–3,700** (1-day car) → **$4,000–7,000+** (3-day car).

## 7. Design

- **Theme:** navy & camel. Deep ink-navy panels, warm off-white text, camel as primary accent, muted gold secondary, muted sage for live states. Earlier red/yellow "racing livery" was rejected as "racecar bedroom" — current palette is the explicit user-approved direction.
- **Header tagline:** "A weekend to remember." Checkered-flag motif replaced with subtle camel dots.
- **Pick colors:** Your pick = slate-blue, His pick = camel. Distinct but both muted.
- Single HTML file, embedded CSS/JS, no build step.

## 8. Persistence

- All user state (edits, selections, toggles) lives in `localStorage` under a versioned key. Bumping the version invalidates old caches when the data model changes.
- **Known limitation:** state is per-device/browser. Two people viewing don't see each other's edits — only their own picks. This is acceptable for the v1 use case; cross-device sync would require a backend.

## 9. Known Constraints & Caveats

- **Images:** hotel photos are hotlinked from Expedia's CDN. Some browsers may block them. Every `<img>` has an `onerror` handler that swaps to a styled SVG placeholder so cards never show broken-image icons. Airbnb neighborhood cards use the placeholder by design (a neighborhood doesn't map to one stable photo).
- **Airbnb listings:** real-time Airbnb inventory is not pullable. The app links out to pre-filtered searches rather than naming specific units.
- **Prices:** all costs are estimates from comparable current listings. The Call buttons route to vendors so users can lock real quotes.
- **Supercar gating:** exotic rental shops typically require driver 25+ with full-coverage insurance and a $3–5k refundable deposit. Not enforced in-app — surfaced as a planner note.

## 10. Deployment

- Static site on Vercel. Root file must be `index.html` so the bare URL resolves with zero config. No build step.
- Repo: `github.com/awrecruiter/SpecialOccasions`.

## 10b. Saturday Plan Revision (Jun 2026)

Saturday Jun 27 was retitled **"Supercharged + Providence Night."** F1 Arcade was removed. New flow:

1. **2:00 PM — Supercharged Entertainment** (~5 hrs, shut it down).
2. **7:30 PM — Train south → Providence.** Drive 15 min to Mansfield Station, MBTA Providence/Stoughton Line southbound (~25 min) to Providence Station.
3. **8:30 PM — Providence nightlife (find your spot).** Open-ended; surface anchors (Federal Hill, The Avery, The Eddy, Hot Club, Courtland Club) rather than locking a single venue. Note last northbound train is typically ~11pm–midnight Saturdays.

`STORE_KEY` and `PREF_KEY` bumped `v3 → v4` so cached visitors load the new seed.

## 11. Open / Deferred

These were discussed but explicitly not built into the artifact pending review:

- **Dinner slot.** Candidates surfaced: STK (upgrade from happy hour), Maple & Ash, Rare Steakhouse at Encore, Contessa (rooftop), Woods Hill Pier 4. Caribbean/Ital direction added later: Rhythm 'n Wraps (Allston, vegan Caribbean) and Fresh Food Generation (Dorchester, locally-sourced Afro-Caribbean) as the closest fits. Decision pending: one marquee dinner + one casual Caribbean/Ital meal in a separate slot.
- **Cost-split / who-pays-what** breakdown on the budget.
- **Booking inquiry message drafts** for jet skis, gun range, car rental (user has not yet asked for these to be generated).
- **User-uploaded photos** as a reliable alternative to hotlinked CDN images.

## 12. Success Criteria

- Both partners can open the link, see the same plan, and each register an independent lodging preference.
- The "where are we now" indicator correctly tracks the active activity during Jun 26–29.
- Toggling the supercar between 1- and 3-day recomputes the grand total live.
- The page loads and is fully interactive on Vercel with no build configuration.
