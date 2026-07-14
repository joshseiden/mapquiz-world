# Map Quiz (mapquiz.world) — Project Brief

**Last updated:** 2026-07-14

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

## Current State

Game is live and feature-complete at lighthearted-kitsune-076422.netlify.app. Repo initialized 2026-07-14; migrating to GitHub-linked Netlify deploy and connecting mapquiz.world via Netlify DNS (in progress).

## Next Steps

- [ ] Link this repo to the Netlify site; verify continuous deploy
- [ ] Connect mapquiz.world (Netlify DNS; nameserver change at DreamHost)
- [ ] Fact-check trivia dataset (Claude-written, approximate) before wide sharing
- [ ] Install Plausible analytics per plan above
- [ ] Decide: clean up Wikipedia intros that end mid-sentence with "…"

## Licenses & Constraints

Natural Earth via world-atlas (public domain), mledoze/countries (ODbL), Wikipedia intros (CC BY-SA 4.0), D3.js (ISC) — all attributed in the game footer. Trivia table is hand-written and approximate (disclaimer in footer). Shared leaderboard would require a backend — out of scope.

## Icebox

- "Review missed" flashcard recap screen (cycle through missed countries until each is answered right twice)
- Explore mode: hover labels between rounds to study the neighborhood of a miss
- Persist deck/history in localStorage across reloads
- Shared leaderboard (needs backend)
