# Map Quiz (mapquiz.world) — Project Brief

**Last updated:** 2026-07-18

This is the living project brief shared between Claude Chat/Cowork (planning) and Claude Code (implementation). Claude Code: read this file at the start of every build session and update the Current State section before ending one.

## Goals

A browser-based geography-learning game: an unlabeled world map highlights a mystery country and the player guesses its name, with trivia hints, scoring, and spaced repetition to reinforce missed countries. Fun, playable, and learning-oriented, covering all 165 independent countries on the 110m world map. Shareable with friends: static files, free hosting, no backend, no accounts.

## Structure

Single-page app: `index.html` contains all game code and embedded data (country intros, trivia table). `vendor/` holds self-hosted dependencies: d3.min.js, topojson-client.min.js, countries-110m.json (map), countries.json (country data).

## Technical Decisions

- Static site, no build step, no backend
- All dependencies self-hosted (supply-chain safety and reliability)
- Hosting: Netlify, continuous deploy from GitHub (this repo)
- Domain: mapquiz.world (purchased 2026-07-14, DreamHost registrar, Netlify DNS)
- Analytics (planned, not yet installed): Plausible + `round_complete` custom event; script tag gated on hostname so local copies stay analytics-free; footer disclosure line
- Country intros are frozen Wikipedia extracts embedded in the file (CC BY-SA credit), not fetched at runtime
- Must be served over HTTP — fetch of vendor JSON fails from file://

## Game Design Decisions

- Typed guesses (not multiple choice), all countries, endless play
- Typo-forgiving matching with ambiguity guard (guess must be closer to the answer than any other country)
- Hints are trivia (agricultural products, then major industries), one per wrong guess; population/capital/etc. at round end
- Scoring 10/5/1 by guess number; Missed counter tracks three-strike losses
- Spaced repetition: misses return in 5–10 rounds, two-hint wins in 15–25, clean wins benched until reshuffle
- Map rotates to center each country; auto-zoom intro; manual zoom/pan; no inset view
- Deck persistence across reloads: declined (in-session no-repeat only)

## Decisions Log

- 2026-07-10 — Deployed to Netlify (lighthearted-kitsune-076422.netlify.app) via drag-and-drop; play-tested clean
- 2026-07-10 — Analytics plan agreed: Plausible over GA4/GoatCounter; domain purchase must come first
- 2026-07-14 — Named the game Map Quiz; purchased mapquiz.world at DreamHost
- 2026-07-14 — Adopted Claude Code workflow: GitHub repo + Netlify continuous deploy + this brief as shared context
- 2026-07-14 — mapquiz.world DNS resolving (nameserver switch propagated)
- 2026-07-14 — Renamed in-game title/header from GeoGuess to MapQuiz.world
- 2026-07-14 — SSL certificate provisioned for mapquiz.world
- 2026-07-15 — Guess/Next-country merged into one two-state button (mobile UX, family play-testing feedback); committed and deployed
- 2026-07-18 — Added footer copyright line (year, name, joshuaseiden.com link) — kept simple, no separate about page
- 2026-07-18 — Built feedback form (Netlify Forms) per 2026-07-15 spec; not yet visually tested in a real browser (no headless browser tooling available in this environment) — recommend a quick manual check before/after this deploy
- 2026-07-18 — Reworded feedback textarea placeholder to "Do you have feedback to share? An idea for an improvement? A problem?"
- 2026-07-18 — Miss-recycling overhaul (full-misses-only, review badge, reveal line) built and play-tested on the live deploy — confirmed working
- 2026-07-19 — "Known for" hint data drafted, reviewed (165 A/B/C choices by Josh), fact-checked, and finalized in known-for.json
- 2026-07-19 — Adopted branch → deploy preview → play-test → merge workflow (see Build Workflow above); direct commits to main retired
- 2026-07-22 — Phase 1 hint overhaul built on `hint-overhaul` branch, PR opened for deploy-preview play-test
- 2026-07-22 — Phase 1 hint overhaul play-tested on deploy preview (confirmed good) and merged to main; branch deleted
- 2026-07-23 — Phase 2 (Learn mode) built on `learn-mode` branch, PR opened for deploy-preview play-test
- 2026-07-23 — Play-test feedback on the deploy preview: unclear wrong-answer copy and a too-subtle header mode toggle — both fixed
- 2026-07-23 — Further play-test feedback: header toggle still unclear — reworked into a segmented control + inline confirm at the bottom of the controls card, welcome overlay back to first-visit-only duty
- 2026-07-23 — Bottom-of-card control fell below the fold on the play path; reframed as a "Hard mode" ON/OFF toggle (Test yourself = an opt-in modifier, not a peer mode) with a header switch + confirm popover, and a simplified single-button welcome overlay

## Current State

Live and feature-complete. Repo: github.com/joshseiden/mapquiz-world, linked to Netlify (continuous deploy from main, verified 2026-07-14). Domain mapquiz.world added as primary (www redirects); Netlify DNS zone created; DreamHost nameservers switched to dns1–dns4.p06.nsone.net on 2026-07-14 and now resolving, with SSL certificate provisioned — mapquiz.world is live over HTTPS. In-game title/header renamed from GeoGuess to MapQuiz.world to match. Phase 1 hint overhaul (known-for/capital/borders hint ladder) merged to main 2026-07-22 — Phase 2 (Learn mode) is next up.

## Build Workflow (standing process, adopted 2026-07-19)

Every feature builds on a branch and is play-tested before it reaches production. Claude Code: follow this on every build session.

1. Read this brief first.
2. Create a feature branch (short kebab-case name, e.g. `hint-overhaul`). If the working copy has uncommitted files, commit them on this branch as the first commit.
3. Implement, commit, push the branch, and open a PR with `gh` (title = feature name, body = one-paragraph summary).
4. Netlify Deploy Previews (on by default) build every PR at `deploy-preview-<PR#>--lighthearted-kitsune-076422.netlify.app` — report this URL so Josh can play-test it (shareable, HTTPS, updates on every push to the branch). Production and mapquiz.world are untouched.
5. Update this brief's Current State on the branch before ending the session.
6. Merge only when Josh says the play-test passed ("merge the PR"). Merging to main triggers the production deploy. Delete the branch after merge.

Never commit directly to main except for trivial brief-only edits.

## Next Steps

- [ ] Fact-check trivia dataset (Claude-written, approximate) before wide sharing
- [ ] Install Plausible analytics per plan above
- [ ] Decide: clean up Wikipedia intros that end mid-sentence with "…"

## Backlog (added 2026-07-15, from family play-testing)

- [x] **Miss-recycling overhaul** (built 2026-07-18, per spec agreed 2026-07-18):
  1. Recycling now triggers only on complete misses (`!won` in `endRound`) — the old branch that also recycled 3rd-guess wins is gone. Return window widened from 5–10 to 8–15 rounds.
  2. Review badge: a `.review-badge` pill (top-left of the map card, same absolutely-positioned-in-`mapWrap` pattern as the zoom controls/points-pop), created once at setup and toggled via `display: flex`/`none`. `isReviewRound` flag is set in `nextCountry()` from `guessHistory.get(current) === "lost"`, exactly per the implementation note in the spec — so it also lights up for a previously-missed country resurfacing via a natural deck reshuffle, not just via the spaced-repetition splice.
  3. Reveal reinforcement line added inside the result banner, gated on `isReviewRound`.
  - Play-tested 2026-07-18 on the live deploy — confirmed working.
- [ ] **Smarter wrong-guess feedback:** replace flat "Not quite" — if the guess is on a different continent, say so; if regionally correct, "not quite"; if within ~2 countries of the answer, "close!" *(Needs design: requires an adjacency/distance measure between countries — discuss approach before building. Note: the `borders` field used by the hint overhaul below provides the adjacency graph for this.)*
- [x] **Phase 1 — Hint overhaul** (built 2026-07-22, per spec agreed 2026-07-18; build before Learn mode — it's the data plumbing):
  1. Curated "known for" data embedded as `KNOWN_FOR` in index.html (next to `TRIVIA`); the standalone `known-for.json` was deleted — index.html is now the source of record. `known-for-factcheck-revised.md` kept in the repo as the review record.
  2. `bordersText()` maps each country's `borders` (ISO alpha-3 codes) to display names via a new `factsByCca3` lookup built alongside the existing `factsById`. Island nations (empty `borders` array) fall back to `"Island nation in " + subregion/region`.
  3. `buildRows()` hint ladder reordered: stage 1 = "Known for", stage 2 = "Capital" + "Borders" (two rows, revealed together on the 2nd wrong guess). Agricultural products, major industries, and population demoted to stage 3 (reveal-time only) — data untouched, just no longer used as hints.
  - Play-tested on the deploy preview 2026-07-22 — confirmed working; merged to main.
- [x] **Phase 2 — Learn mode** (built 2026-07-23, spec v2 agreed 2026-07-22, supersedes 2026-07-18 draft; requires Phase 1 ✓). On-ramp for players who struggle even on well-known countries. Design principle: **Test yourself is the destination; Learn mode is a moving walkway toward it** — recognition converts to recall per country, seamlessly.
  - **Welcome screen overlay (the mode picker):** `#modeOverlay` reuses the `.feedback-overlay`/`.feedback-panel` classes (compound class, so the backdrop/open-state CSS isn't duplicated) plus new `.mode-panel`/`.mode-cards`/`.mode-card` rules. Same overlay serves first-visit welcome, mode choice, and the footer "Switch mode" reopen (current mode preselected, Start button relabeled "Switch", reset note shown) — one `openModeOverlay(preselect, isSwitching)` function drives all three, exactly per spec.
  - **Mode persistence:** `localStorage["mapquiz-mode"]`, `loadMode()`/`saveMode()` wrapped in try/catch.
  - **Mode switch:** `applyModeSwitch()` resets only `score` and `round` (Correct/Missed stay cumulative across the whole session — read "different currencies per mode" as applying to score specifically, since Correct/Missed are just counts). Deck reconciliation is a full rebuild — `remaining = shuffle(computeDeckPool())` — rather than a surgical merge that preserves pending miss-recycle timing; simpler, and matches the spec's "expands/contracts the deck" language, but means a country mid-recycle at the moment of a switch loses its scheduled return slot and just reshuffles in normally. `guessHistory`/`mapTint`/`learnState` (map tints + graduation progress) are untouched by a switch, so they carry over exactly as spec'd.
  - **Round format (multiple choice):** implemented as spec'd — `#learnQuestion` banner + `#answerGrid` (4 buttons) replace `#guessRow`/`#guesses` for `roundKind === "tap"` rounds; resolving a tap calls the same `endRound()` used everywhere else.
  - **Per-country graduation:** `learnState` (Map keyed by map feature, same key as `guessHistory`) tracks `{tapCorrect, graduated}` per country. 2 tap-correct wins sets `graduated = true`; the next round for that country is `roundKind = "graduation"` — **implementer's choice:** this reuses the full Classic 3-guess flow verbatim (hint ladder, dots, wrong chips) rather than a single-shot typed attempt, with `endRound()` overriding scoring to a flat +10 on any win and un-graduating (`tapCorrect = 0`) on a loss. Chosen because it's the smallest diff, reuses `submitGuess()`/`buildRows()` untouched, and fits "moving walkway" — graduating literally means "this round now plays like Classic." Mastery (`masteredSet`) is session-only, per spec.
  - **Two-tier map tints:** new `mapTint` (feature → `"won"|"tap"|"lost"`), replacing `guessHistory` as what `countryClass()` reads for display (`guessHistory` still exists, driving recycling + the review badge, unchanged). New `.country.done-tap { fill: #26443a }` for the soft-green tap tint, sitting between the land color and the existing `.done-won` green.
  - **Starter deck & growth:** `STARTER_DECK` (54 codes) embedded verbatim. Growth order is **not** a hand-curated prominence list — it's the remaining 111 countries sorted by the `TRIVIA` population field already in the file (descending), which is a reasonable "prominence/population" proxy without a second curation pass. Every 3rd Learn-mode correct answer (tap or graduation) grows the deck by one.
  - **Distractors:** `pickDistractors()` — past 10 Learn-mode corrects, same-subregion candidates first; otherwise (or to fill remaining slots) "famous" = `STARTER_DECK` members from a different region than the target, then a random fallback.
  - **Stats:** Mode stat tile added to the header (leftmost, before Score).
  - **Not live-tested:** the tap→graduation→mastery transition specifically — it needs 2 non-consecutive tap-wins on the *same* country, which under natural shuffling can take up to ~54 rounds to line up, so it wasn't practical to exercise end-to-end in this session. Everything else (mode picker, both round types individually, mode switching mid-game with map-tint/counter persistence, localStorage persistence across reload, deck-growth data, no console errors) was play-tested locally. Recommend Josh either spot-checks graduation on the deploy preview (get 2 correct taps on the same country — it'll resurface once the deck reshuffles) or asks for a longer automated soak test before merge.
- [x] **Mode-switch rework** (built 2026-07-23, spec agreed 2026-07-23, from `learn-mode` branch play-testing — the header "Learn | Test" pill added mid-branch doesn't communicate what it does):
  1. Header toggle pill removed; the Mode stat tile is back (leftmost, before Score) as a passive label, updated by `updateModeLabel()`.
  2. New `.mode-switch-bar` at the bottom of the controls card (`#modeSeg`, two `.mode-seg-btn`s: "Learn the map" / "Test yourself"), separated by a hairline top border, full width, active segment filled amber.
  3. Tapping the inactive segment reveals `#modeConfirm` ("Switching resets your score but keeps your map — **switch now** · cancel") instead of switching immediately; "switch now" calls `applyModeSwitch()` (unchanged), "cancel" dismisses via `hideModeConfirm()`. Tapping the already-active segment no-ops. `nextCountry()` also calls `hideModeConfirm()`, so starting a new round implicitly cancels a dangling confirm rather than leaving it stranded.
  4. `openModeOverlay()` simplified back to first-visit-only (no `isSwitching`/preselect args, no switch-note, always labeled "Start"); backdrop-click-to-dismiss removed since the overlay is mandatory again on first visit. `applyModeSwitch()` itself is unchanged from Phase 2.
  - Play-tested locally: segment click → confirm → switch now (mode/score/round update, new round starts, confirm auto-hides) and segment click → cancel (no-op, confirm hides) both work; no console errors.
  - **SUPERSEDED by v2 below** — Josh's play-test found the bottom-of-card control falls below the fold (the answer buttons are the end of the play path; nobody scrolls past them).
- [x] **Mode-switch rework, v2 — "Hard mode" toggle** (built 2026-07-23, spec agreed 2026-07-23; supersedes the bottom-of-card segmented control above). Reframe: the modes are no longer two peers — **Learn is simply "the game"; Test yourself is "Hard mode," an opt-in modifier.** UI vocabulary changes to Hard mode ON/OFF; internal mode names/state stay exactly as built (`mode = "learn"|"test"`, `mapquiz-mode` localStorage key, `applyModeSwitch()`/`computeDeckPool()` untouched) — only the presentation layer changed.
  1. Header switch (`#hardModeSwitch`) replaces the Mode stat tile, leftmost in the stats cluster: "HARD MODE" label, a CSS-only on/off switch (`.hard-mode-switch`/`.hard-mode-knob`, `.on` toggles track/knob color and knob position), and a state word (`#hardModeState`, "OFF"/"ON"). `updateHardModeUI()` (renamed from `updateModeLabel()`) derives switch state from `mode === "test"`.
  2. Bottom-of-card `.mode-switch-bar`/`#modeSeg`/`#modeConfirm` control from the previous rework fully removed (HTML, CSS, and JS).
  3. `#hardModePopover` anchored below the switch (`position:absolute`, parent `.hard-mode-stat` is `position:relative`) with "flip it"/"cancel" — reuses the same pending-state-then-confirm pattern as the old segmented control (`pendingHardModeFlip` instead of `pendingSwitchMode`), including `nextCountry()` calling `hideHardModePopover()` so a fresh round dismisses a dangling popover.
  4. Welcome overlay simplified: no more mode cards, just heading + two intro paragraphs (spec copy verbatim) + a single always-enabled Start button. Clicking Start hardcodes `mode = "learn"` (Hard mode OFF) — there's no longer a choice to make on first visit.
  - Play-tested locally: switch → popover → flip it (mode/switch UI/round all update, popover dismisses) and switch → cancel (no-op) both work; Hard mode ON persists across reload via the existing `mapquiz-mode` localStorage key; no console errors.
- [x] **Increase border contrast** (done 2026-07-15: stroke #4a5878 at 0.5 — lighter grey-blue, target country untouched)
- [x] **Feedback form via Netlify Forms** (built 2026-07-18, per spec agreed 2026-07-15) — form markup is static in index.html (Netlify's build-time HTML parser needs to see it, so it's CSS-hidden, not JS-injected); overlay opens from a "Feedback" link in the footer, submits via fetch, shows a thank-you state without navigating away. Fixed a bug found while implementing: the existing global Enter-key "advance to next round" listener didn't check whether the feedback overlay was open, so pressing Enter while typing feedback (after a round had ended) could have advanced the game underneath the modal — now gated on the overlay's open state.
  - Netlify dashboard config done 2026-07-18: form detection enabled (was off — account-level default; required a redeploy for the form to register), "feedback" form active, email notification → joshseiden@gmail.com (subject "MapQuiz.world feedback"). End-to-end test confirmed: form → dashboard → inbox.

## Licenses & Constraints

Natural Earth via world-atlas (public domain), mledoze/countries (ODbL), Wikipedia intros (CC BY-SA 4.0), D3.js (ISC) — all attributed in the game footer. Trivia table is hand-written and approximate (disclaimer in footer). Shared leaderboard would require a backend — out of scope.

## Icebox

- **Per-continent stats** ("Africa: 4/23") — for players who enjoy Classic difficulty but have regional blind spots; makes weak spots visible and gives Learn mode a warm-up use for strong players
- **Reverse mode** ("Find Brazil!" — game names the country, player taps it on the map) — most kid-native interaction, but a bigger build: tap-target logic + own scoring. Its own game, not an easier version of this one
- **Famous-people hints** — high resonance for teens but dates quickly and skews by generation; revisit as flavor after the "known for" line ships
- **Mobile layout pass** (added 2026-07-14 after family play-testing on iPhones/iPad): sidebar reveal content stacks below the map on narrow screens; review viewport sizing, touch targets, and reveal-content placement for phone-first play. The guess/next button merge (2026-07-14) was the first step.
- "Review missed" flashcard recap screen (cycle through missed countries until each is answered right twice)
- Explore mode: hover labels between rounds to study the neighborhood of a miss
- Persist deck/history in localStorage across reloads
- Shared leaderboard (needs backend)
