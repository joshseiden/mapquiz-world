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

## Current State

Live and feature-complete. Repo: github.com/joshseiden/mapquiz-world, linked to Netlify (continuous deploy from main, verified 2026-07-14). Domain mapquiz.world added as primary (www redirects); Netlify DNS zone created; DreamHost nameservers switched to dns1–dns4.p06.nsone.net on 2026-07-14 and now resolving, with SSL certificate provisioned — mapquiz.world is live over HTTPS. In-game title/header renamed from GeoGuess to MapQuiz.world to match. From now on, build work happens in Claude Code sessions: read this brief first, commit and push (push triggers deploy), update this brief before ending.

## Next Steps

- [ ] Fact-check trivia dataset (Claude-written, approximate) before wide sharing
- [ ] Install Plausible analytics per plan above
- [ ] Decide: clean up Wikipedia intros that end mid-sentence with "…"

## Backlog (added 2026-07-15, from family play-testing)

- [x] **Miss-recycling overhaul** (built 2026-07-18, per spec agreed 2026-07-18):
  1. Recycling now triggers only on complete misses (`!won` in `endRound`) — the old branch that also recycled 3rd-guess wins is gone. Return window widened from 5–10 to 8–15 rounds.
  2. Review badge: a `.review-badge` pill (top-left of the map card, same absolutely-positioned-in-`mapWrap` pattern as the zoom controls/points-pop), created once at setup and toggled via `display: flex`/`none`. `isReviewRound` flag is set in `nextCountry()` from `guessHistory.get(current) === "lost"`, exactly per the implementation note in the spec — so it also lights up for a previously-missed country resurfacing via a natural deck reshuffle, not just via the spaced-repetition splice.
  3. Reveal reinforcement line added inside the result banner, gated on `isReviewRound`.
  - **Not yet visually tested in a real browser** (same environment limitation as the feedback form — no headless browser tooling available); verified by tracing the logic and confirming the JS parses, but worth a play-test pass, especially the badge's timing/visibility across a reshuffle.
- [ ] **Smarter wrong-guess feedback:** replace flat "Not quite" — if the guess is on a different continent, say so; if regionally correct, "not quite"; if within ~2 countries of the answer, "close!" *(Needs design: requires an adjacency/distance measure between countries — discuss approach before building.)*
- [x] **Increase border contrast** (done 2026-07-15: stroke #4a5878 at 0.5 — lighter grey-blue, target country untouched)
- [x] **Feedback form via Netlify Forms** (built 2026-07-18, per spec agreed 2026-07-15) — form markup is static in index.html (Netlify's build-time HTML parser needs to see it, so it's CSS-hidden, not JS-injected); overlay opens from a "Feedback" link in the footer, submits via fetch, shows a thank-you state without navigating away. Fixed a bug found while implementing: the existing global Enter-key "advance to next round" listener didn't check whether the feedback overlay was open, so pressing Enter while typing feedback (after a round had ended) could have advanced the game underneath the modal — now gated on the overlay's open state.
  - Netlify dashboard config done 2026-07-18: form detection enabled (was off — account-level default; required a redeploy for the form to register), "feedback" form active, email notification → joshseiden@gmail.com (subject "MapQuiz.world feedback"). End-to-end test confirmed: form → dashboard → inbox.

## Licenses & Constraints

Natural Earth via world-atlas (public domain), mledoze/countries (ODbL), Wikipedia intros (CC BY-SA 4.0), D3.js (ISC) — all attributed in the game footer. Trivia table is hand-written and approximate (disclaimer in footer). Shared leaderboard would require a backend — out of scope.

## Icebox

- **Easier modes** — open question from play-testing: what would an easier mode look like? (e.g., multiple choice, continent-limited decks, hint-first mode — for younger players)
- **Mobile layout pass** (added 2026-07-14 after family play-testing on iPhones/iPad): sidebar reveal content stacks below the map on narrow screens; review viewport sizing, touch targets, and reveal-content placement for phone-first play. The guess/next button merge (2026-07-14) was the first step.
- "Review missed" flashcard recap screen (cycle through missed countries until each is answered right twice)
- Explore mode: hover labels between rounds to study the neighborhood of a miss
- Persist deck/history in localStorage across reloads
- Shared leaderboard (needs backend)
