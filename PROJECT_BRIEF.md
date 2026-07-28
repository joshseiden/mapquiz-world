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
- 2026-07-23 — Phase 2 (Learn mode) play-tested on deploy preview (confirmed good, including the mode-switch reworks) and merged to main; branch deleted
- 2026-07-28 — Viewport-aware layout built on `viewport-layout` branch (flex-column page + flexing map card + never-shrinks controls card, plus a padding/gap compaction pass), PR opened for deploy-preview play-test
- 2026-07-28 — First MacBook play-test found it still cut off (iPhone was fine); root-caused with a local headless-Chrome harness to a `min-height` vs `height` flex ambiguity plus the map's `<svg>` feeding its aspect ratio into layout sizing; fixed properly (`.svg` taken out of flow via `position:absolute`, restoring a real dynamic protective floor instead of guessed pixel constants) and re-verified across the full test matrix plus a forced extreme-length-text stress case
- 2026-07-28 — Second play-test (iPad) found the map expanding and pushing "Next country" below the fold, landscape only, after a correct guess; root cause was the same protective floor stretching the map+controls column to match the sidebar's full post-guess reveal in two-column mode — fixed by making that floor mobile-stacked-layout-only (row/two-column mode doesn't need it, since the sidebar scrolls independently there) and re-verified via the CDP harness
- 2026-07-28 — Viewport-aware layout play-tested clean on the deploy preview (MacBook + iPad, both the original cut-off and the iPad-landscape follow-up confirmed fixed) and merged to main; branch deleted
- 2026-07-28 — Plausible analytics built on `analytics` branch per spec (hostname-gated snippet, `round_complete` custom event, footer disclosure); PR opened for deploy-preview play-test
- 2026-07-28 — Plausible analytics merged to main; live on mapquiz.world. Dashboard/"Verify Script installation" check still needs a Josh-side confirmation (no dashboard access from this environment)

## Current State

Live and feature-complete. Repo: github.com/joshseiden/mapquiz-world, linked to Netlify (continuous deploy from main, verified 2026-07-14). Domain mapquiz.world added as primary (www redirects); Netlify DNS zone created; DreamHost nameservers switched to dns1–dns4.p06.nsone.net on 2026-07-14 and now resolving, with SSL certificate provisioned — mapquiz.world is live over HTTPS. In-game title/header renamed from GeoGuess to MapQuiz.world to match. Phase 1 hint overhaul (known-for/capital/borders hint ladder) merged to main 2026-07-22. Phase 2 (Learn mode, with the "Hard mode" toggle for switching to Test yourself) merged to main 2026-07-23 — both game modes are live. Viewport-aware layout (flex-column page, flexing map card, controls card that's always fully visible) play-tested clean and merged to main 2026-07-28 — answer buttons and the input row no longer get cut off at the bottom of the screen on any device tested (laptop, iPhone, iPad, both orientations). Plausible analytics merged to main and live on mapquiz.world 2026-07-28 — hostname-gated so local copies/previews stay analytics-free, `round_complete` custom event on every round, footer disclosure line. Still needs a Josh-side check of the Plausible dashboard (script verification, event arrival) since this environment has no dashboard access.

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
- [x] **Install Plausible analytics** (spec updated 2026-07-24 — original 7/10 plan predates Learn mode):
  - **Prerequisite DONE (2026-07-24):** Plausible account created, site registered. Plausible issued its newer per-site snippet (not the classic `data-domain` tag). Snippet to embed:
    ```html
    <!-- Privacy-friendly analytics by Plausible -->
    <script async src="https://plausible.io/js/pa-7hvi0u-qYlTLl86CS2DoX.js"></script>
    <script>
      window.plausible=window.plausible||function(){(plausible.q=plausible.q||[]).push(arguments)},plausible.init=plausible.init||function(i){plausible.o=i||{}};
      plausible.init()
    </script>
    ```
  - **Hostname gate (updated for the new snippet):** the per-site script must not LOAD at all off-domain. Implementation: one inline script that checks `location.hostname === "mapquiz.world"` (also allow `www.`) and only then dynamically appends the plausible `<script async src=…>` tag, defines the stub, and calls `plausible.init()`. Local copies and deploy previews send nothing. (Exception to the self-hosted-dependencies rule: the analytics script intentionally loads from plausible.io — it's the product.)
  - **One custom event, `round_complete`,** fired in `endRound()` via `window.plausible('round_complete', { props: { mode, kind, result } })` — `mode` ("learn"/"hard"), `kind` ("tap"/"typed"/"graduation"), `result` ("won"/"lost"). The queue stub makes this safe even before the script loads; when gated off, define a no-op `window.plausible` so call sites need no conditionals. (If props prove Growth-only after trial and Starter is chosen, fall back to the bare event count.)
  - Note: Josh enabled Plausible's optional outbound-link / file-download / form-submission auto-tracking in the site config — no code needed for those; the form-submission one will incidentally count feedback-form submits.
  - Plausible's "Verify Script installation" check will only pass against production (the gate blocks everything else) — run it after merge.
  - **Footer disclosure line:** "Privacy-friendly analytics by Plausible — no cookies, no personal data. A game preference is stored on your device."
  - Build on a branch per standing workflow; verify events arrive in the Plausible dashboard from the production deploy (previews are gated off, so final verification happens post-merge on mapquiz.world).
  - **Built 2026-07-28** on `analytics` branch, per spec above: hostname-gated snippet in `<head>` (checks `/^(www\.)?mapquiz\.world$/`, defines a no-op `window.plausible` off-domain, otherwise defines the real queue stub, calls `plausible.init()`, and injects the `pa-7hvi0u-…` script); `round_complete` fires unconditionally at the top of `endRound(won)`. One deviation from the spec text: the `kind` prop uses the actual `roundKind` values from the code (`"tap"`, `"classic"`, `"graduation"`) rather than the spec's `"typed"` — `"classic"` is what the code calls the typed-guess flow (used by both Hard mode and graduation rounds), and there's no separate `"typed"` value to send. `mode` is mapped from the internal `"test"` value to `"hard"` to match the spec's prop values and the UI's "Hard mode" label. Footer disclosure line added verbatim.
  - **Play-tested locally** via a local headless-Chrome CDP harness (same approach as the viewport-layout fixes): confirmed on `localhost` (off-domain) `window.plausible` is the no-op stub, no `plausible.io` script tag is injected and no network request to it fires; confirmed `round_complete` fires with correct props for both a Learn-mode win (`{mode:"learn",kind:"tap",result:"won"}`) and loss (`{mode:"learn",kind:"tap",result:"lost"}`); no console errors.
  - **Merged to main and live on mapquiz.world 2026-07-28.** Outstanding, Josh-only (no Plausible dashboard access from this environment): run the "Verify Script installation" check in the Plausible site settings, and confirm `round_complete` events with their `mode`/`kind`/`result` props are actually arriving in the dashboard from real production traffic.
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
- [x] **Viewport-aware layout** (spec agreed 2026-07-24 — answer buttons cut off at the bottom of the screen in Learn mode; this is the structural fix, and the core of the icebox "mobile layout pass"):
  1. **Flex-column page layout:** header (fixed height) + main play area fills the rest of the viewport. Use `100dvh`, not `100vh` (iOS browser chrome). The **map card flexes** (`flex: 1`, `min-height: ~320px`); the **controls card keeps its natural height and is always fully visible** — question line + all 4 answer buttons in Learn mode, input row + dots in Hard mode. The map absorbs all remaining space; SVG scales cleanly and auto-zoom already centers the target, so a shorter map is fine.
  2. **Desktop two-column:** the left column (map + controls) obeys rule 1; the sidebar may scroll independently if its reveal content exceeds the viewport. On narrow/stacked layouts, same principle for map + controls; the sidebar continues to stack below (its reveal-time placement remains an icebox item — this fix is about the play controls, which must never be cut off).
  3. **Compaction pass ("pinch"):** tighten answer-button padding, known-for banner, and vertical gaps modestly — but never below **44px tap-target height** on the buttons (iOS guideline; the audience is phone-first kids).
  4. **Test matrix before PR:** ~13" laptop (~800px viewport height), iPhone (regular + SE-class height), iPad — both modes, mid-round and reveal states, plus the welcome overlay.
  - **Built 2026-07-28** (per spec above, all 4 points): `body` is a flex column, `header` fixed, `.layout` as `flex:1`, `.main` (map+controls) a nested flex column, `.map-wrap` at `flex:1; min-height:320px`, `.controls` at `flex-shrink:0`. `.sidebar` stretches to match column height (`align-items:stretch`) and scrolls independently (`overflow-y:auto`) when long. Compaction pass: `.answer-btn` padding 16px→13px (~46px, clear of the 44px floor), tighter gaps throughout.
  - **First deploy-preview play-test (Josh, MacBook) found it still broken** — answer buttons cut off — while iPhone looked fine. Root-caused with a local headless-Chrome harness (no project `run` skill existed for this static site; recommend `/run-skill-generator` if this comes up again) measuring actual computed layout at several viewports:
    1. `body { min-height: 100dvh }` isn't a hard cap — when body's own auto-height calculation is otherwise indeterminate, Chrome fell back to sizing the flex-grow row from content rather than the viewport, and `.map-wrap`'s `<svg>` (percentage-sized, but carrying its `viewBox`'s 1.92:1 aspect ratio) fed straight into that calculation, re-inflating the map to `width / 1.92` regardless of available height — the exact cut-off the fix was supposed to prevent. Fix: `body { height: 100dvh }` (definite, not a floor).
    2. That alone broke the **stacked mobile layout**: with a hard body height and `.main`'s min-height at `0`, a tall sidebar (row mode) or the stacked sidebar/footer (mobile, sharing `.main`'s own vertical axis) could squeeze `.main` shorter than `map-wrap(320px) + controls` actually need — the overflow doesn't push the page taller, it invisibly overlaps whatever comes next (confirmed on iPhone SE: sidebar facts and footer text rendered on top of the answer grid).
    3. Fix: give `.layout`/`.main` back their default `auto` min-height (a real protective floor matching current content) instead of `0` — safe now because the `<svg>` was switched from `width/height:100%` to `position:absolute; inset:0`, taking it out of flow so its aspect ratio can no longer feed the "automatic content-based minimum size" calculation that caused point 1. This is what let the fix be *correct* rather than tuned to fixed pixel guesses: `.main` now dynamically matches whatever height its content actually needs each round (verified by injecting an artificially 3-line-long "known for" string — `.main` and the sidebar below both grew to accommodate it, no overlap) rather than a static floor that would work for the common case and silently overlap on a long outlier.
  - **Play-tested in this session** via a local headless-Chrome CDP harness (Chrome installed but no automation packages available, so driven directly over the DevTools protocol from a small Node script — Playwright/Puppeteer aren't installed in this environment) across the full point-4 test matrix (13" laptop incl. a deliberately tight 740px-tall case, iPhone regular, iPhone SE, iPad; welcome/mid-round/reveal states; a forced extreme-length text stress case) — confirmed the answer grid and input row are always fully visible with zero scroll, and the sidebar/footer scroll in gracefully rather than overlapping when content runs long.
  - **Second deploy-preview play-test (Josh, iPad) found a further bug, landscape-only:** after a correct guess, the map visibly expanded and pushed "Next country" below the fold; portrait was fine. Root cause: point 3 above's `auto` min-height on `.layout`/`.main` is a double-edged sword in row/two-column mode (desktop, iPad landscape) — `align-items:stretch` sizes `.main` to match whichever of it or `.sidebar` is tallest, and the sidebar's full post-guess reveal (flag/intro/all facts) is much taller than the pre-guess "??" placeholders, so `.main` (and map-wrap via its `flex:1`) stretched to match it, pushing controls past the viewport. Confirmed via the CDP harness: `.main`'s height moved in exact lockstep with `.sidebar`'s (636px → 893px, identical both sides) after a correct guess at 1180×820, and did *not* reproduce at 820×1180 (portrait stacks the sidebar below `.main` instead of beside it, so they no longer share a stretch axis).
    - **Fix:** the `auto` protective floor is only actually needed in the *stacked* mobile layout (where `.main` and `.sidebar` compete for the same vertical axis) — in row/two-column mode, `.sidebar` has its own `overflow-y:auto` for exactly this situation, so `.main`/`.layout` should stay capped at the viewport-derived height instead of stretching to match a tall sidebar. `.layout`/`.main` min-height is now `0` at the base (row-mode) declaration, with the existing `@media (max-width:900px)` block overriding both back to `auto` for the stacked layout where the floor is actually needed.
    - Re-verified via the CDP harness: iPad landscape now holds `.main` at an identical height before/after a correct guess (636.2px both times, controls fully within the 820px viewport); portrait and iPhone SE still shrink/grow correctly with the sidebar scrolling independently, no regression.
  - **Play-tested clean by Josh on the deploy preview** (MacBook + iPad, both orientations) 2026-07-28 and merged to main; branch deleted.
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
