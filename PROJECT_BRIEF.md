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
- [ ] **Phase 2 — Learn mode** (spec v2 agreed 2026-07-22, supersedes 2026-07-18 draft; requires Phase 1 ✓). On-ramp for players who struggle even on well-known countries. Design principle: **Test yourself is the destination; Learn mode is a moving walkway toward it** — recognition converts to recall per country, seamlessly.
  - **Welcome screen overlay (the mode picker):** an overlay (reuse the feedback-overlay modal pattern) that welcomes the player, describes the game in 1–2 sentences, and offers the two mode choices as large tappable cards, each with a one-sentence description. Selecting a card enables a **Start** button. Draft copy (Josh may refine at preview):
    - Heading: "Welcome to MapQuiz.world" · Intro: "A mystery country lights up on the map — can you name it? Hints help when you're stuck, and countries you miss come back until you know them."
    - **Learn the map** — "Tap the right answer from four choices, and graduate to typing as you master each country."
    - **Test yourself** — "Type your answers cold: three guesses, a hint after each miss, all 165 countries."
    - Behavior: shown on first visit (no preselection). Returning visits skip it and load the remembered mode. The footer "Switch mode" link reopens this same overlay (current mode preselected, Start reads "Switch") with the note "Switching resets your score but keeps your map." One overlay serves welcome, mode choice, and mode switch — no separate confirm dialog.
  - **Mode persistence:** chosen mode saved in localStorage (exactly ONE key, e.g. `mapquiz-mode`; wrap writes in try/catch for private browsing).
  - **Mode switch is non-destructive:** switching changes the input method, not the world. Per-country history, map tints, and graduation state carry across; only score and round counter reset (different currencies per mode). Confirm line: "Switching resets your score but keeps your map." Deck reconciliation: Learn → Test expands the deck to all 165 (mastered countries stay benched); Test → Learn contracts to starter set ∪ countries already encountered this session.
  - **Round format (multiple choice):** the "known for" line displays up front as the question; map highlights the country as usual; the text input row is replaced in the same layout space by **4 tappable answer buttons** (2×2 grid on phones, thumb-sized). One tap resolves: correct → +5 and celebration; wrong → tapped button red, correct button green, then normal reveal. Reveal sidebar unchanged from Classic (known-for is spent as the question; capital/borders/facts show as usual).
  - **Per-country graduation (recognition → recall):** a country answered correctly by tap **2 times** returns as a **typed round** — same typed input as Classic, flagged with a promotion pill in the review-badge position: "★ You know this one — type it!" Typed correct → **+10** (recall pays double) and the country is benched as mastered. Typed wrong → no spiral: it returns as multiple choice next time. Graduation state is per-session for v1 (localStorage stays mode-only; deck persistence remains a separate, deliberate future decision).
  - **Two-green mastery tints on the map (Learn mode):** soft green fill once a country has been answered correctly by tap (new CSS class, lighter than the current done-won tint); full green (current done-won or stronger) once answered correctly by typing. Muted red for misses unchanged. The map doubles as the progress display — pale green spreads as you recognize, saturates as you master.
  - **Recycling:** wrong answers (tap or typed) recycle via the existing miss logic (8–15 rounds) and get the existing review badge — unchanged.
  - **Starter deck (Learn mode ONLY — Test yourself always uses all 165):** the locked `STARTER_DECK` list, 54 countries, reviewed by Josh 2026-07-22. Embed in index.html as:
    `USA, CAN, MEX, BRA, ARG, CHL, COL, PER, CUB, PAN, GBR, IRL, FRA, DEU, ITA, ESP, PRT, NLD, BEL, CHE, AUT, GRC, SWE, NOR, POL, UKR, RUS, TUR, CZE, HUN, ROU, CHN, JPN, IND, KOR, PRK, VNM, THA, IDN, PHL, PAK, AFG, IRN, IRQ, ISR, SAU, EGY, MAR, NGA, KEN, ETH, ZAF, AUS, NZL`
    **Deck growth:** every 3 correct answers, quietly add one new country from the remaining pool (ordered roughly by prominence/population). No difficulty setting to change — the ramp is built in.
  - **Distractors:** early game, pick 3 wrong options from famous, far-apart countries; once a player has ~10 correct, prefer same-subregion distractors when available (difficulty slope inside the mode).
  - **Stats:** same header; show the current mode name next to the score (scores aren't comparable across modes); Missed counts wrong taps and failed typed rounds.
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
