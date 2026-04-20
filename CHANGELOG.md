# Changelog

All notable changes to /recursive will land here.

Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and this project uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-04-20

### Added
- Confirmation turn after Q4 — /recursive now echoes what it caught and asks "nail it, or tell me what I got wrong" before the bullets land on the draft card. Fixes a class of late-surprise errors the redact loop couldn't catch.
- Version footer on the card (`recursive v1.1.0`, right-aligned under the Recursive ID).
- LICENSE file (MIT).
- CHANGELOG.md (this file).
- FAQ block in README covering "card feels wrong", "didn't hear back", and "found a bug".
- Byline in README (`By Paul Schraven` + LinkedIn link) so cold-landing visitors have a trust signal before curling.
- Post-install verify line in README.

### Changed
- Install command now pulls from `https://github.com/.../releases/latest/download/recursive.md` (with `-L` to follow the GitHub redirect), not `raw.githubusercontent.com/.../main/`. Users get a tagged, pinned artifact instead of whatever happens to be on `main`.
- QR and apply link reordered: `Apply: <URL>` is now the primary CTA, printed first. The QR sits below as "or scan from another device" so same-laptop applicants click through instead of squinting at a QR they can't scan without a phone.
- Apprentice and Pragmatist verdict copy rewritten for share-worthy tone. Apprentice no longer reads like rejection ("Early, but you showed up on the right night. Forward this to whoever showed you."). Pragmatist now says what it actually is ("The kind of builder everyone actually wants in the group chat.").
- Step 0 acknowledgement parsing is explicit instead of "read the user's reply intelligently" — affirmative / question / explicit no, each with defined handling. Removes LLM-fragility.
- MCP scan replaced the `grep "command"` fallback (which counted hook definitions as MCP servers) with jq-based parsing of `~/.claude.json` and `~/.claude/settings.json`. Dropped the `$(pwd)/.mcp.json` and `$(pwd)/.claude/settings.json` paths that referenced arbitrary working directories. If jq is missing and `claude mcp list` fails, the scan now honestly returns `?` and the reaction line acknowledges it instead of silently lying with an over-count.
- Q1 option 5 shortened from 3 lines to 2 ("Infrastructure writes the software. Work runs from Slack, Linear, cron, webhooks, customer events."). Less visual intimidation, same punch.
- Q2, Q3, and Q4 now have explicit re-ask-once logic for empty / out-of-range / unparseable responses. Previously only Q1 had it.
- Card and Step 0 ASCII boxes now use the current subline ("experienced leaders shaping AI-native organizations, one commit at a time"), unified with README and the skill's Brand declaration. Previously the boxes still said "experienced builders constructing AI-native organizations" — stale pre-rebrand copy.
- README hero line changed from "your stack signature is your fingerprint, your fingerprint is your invite" (two claims the mechanism doesn't actually support) to "your card is your ticket."
- Hash suffix in the Recursive ID is now documented as a vanity suffix, not a fingerprint. Dropped "unfakeable" and "deterministic per setup" framing — the hash changes when CLAUDE.md is edited or a new project is opened, and the skill is public on GitHub so anyone can reverse the 2-line hash. Anti-abuse is on the admin review side, which is where it always was in practice.

### Fixed
- Placeholder text `[STATIC_QR_BLOCK_PLACEHOLDER]` and `Apply directly: [FORM_LINK_PLACEHOLDER]` that had shipped in the final card output. Replaced with the baked QR and the live Tally URL (`https://tally.so/r/1A2xb1`). This was a P0 — every user running /recursive between v1.0.0 and this release got a broken card. (Already shipped in commit `f8fe518`, formalized here as part of v1.1.0.)

## [1.0.0] - 2026-04-20

Initial release. Six archetypes, four questions, one card. The function that calls itself.
