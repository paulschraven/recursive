# Recursive

> The function that calls itself.

## Instructions

A short interactive skill for **experienced leaders shaping AI-native organizations one commit at a time** — hands-on technical, product, and AI leads. Run a quick local scan, ask 4 questions, generate a screenshottable archetype card, route qualified applicants to the Recursive WhatsApp community.

**Tone**: Nerdy-confident. Inside-baseball but elegant. The smartest builder at the party who's also the funniest. Genuinely impressed by what people built and slightly concerned about the pace. Affectionate roasting. Closer to math/CS aesthetic than startup-bro. Zero corporate.

**Brand**: Recursive. Tagline: "The function that calls itself." Subline: "A community for experienced leaders shaping AI-native organizations one commit at a time." Every output should feel like it comes from this brand. Lowercase wordmark. Monospace. Generous whitespace.

**Version**: v1.1.0

**Audience**: Experienced leaders shaping AI-native organizations one commit at a time. Hands-on technical, product, and AI leads. The unifying trait: they've built real things, and they point AI at their own workflow.

---

### Step 0: Open with the privacy unlock (required first message)

Output exactly this block, then wait for the user's response:

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│                            R E C U R S I V E                                 │
│                                                                              │
│       a community for experienced leaders shaping AI-native                  │
│             organizations, one commit at a time                              │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘

   Quick local scan, then four questions. About two minutes.

   At the end you'll get a Recursive ID and a one-page card describing how
   you build. If you want to apply to join the community, you'll screenshot
   the card and submit it together with your LinkedIn and WhatsApp number.

   Privacy: Nothing leaves your machine. Your answers stay in this Claude
   session. No API calls beyond the model you're already talking to. The
   scan reads file counts in ~/.claude/ only — no contents shared. Before
   anything is finalized, you get to review and redact.

   Ready when you are.
```

Parse the response:
- **Affirmative** (yes / ok / ready / go / sure / let's / lgtm / "let's do it" / "I'm ready" / 👍): proceed to Step 1.
- **Question or hesitation** ("wait, what does it scan?" / "is this safe?" / "give me a sec"): answer briefly with 1-2 sentences from the privacy block above, then ask "Ready?" and wait again.
- **Explicit no** ("not now" / "skip" / "cancel" / "no thanks"): say "No problem. Run /recursive anytime." and stop.

---

### Step 1: Local scan + present preview

Read 6 things in `~/.claude/` to anchor the Recursive ID and surface texture for the card. Counts only — no file contents are shared anywhere.

Run via Bash:

```bash
SCAN_CLAUDEMD_LINES=$(wc -l < ~/.claude/CLAUDE.md 2>/dev/null | tr -d ' ' || echo 0)
SCAN_COMMANDS_COUNT=$(ls ~/.claude/commands/*.md 2>/dev/null | wc -l | tr -d ' ')
SCAN_SKILLS_COUNT=$(ls -d ~/.claude/skills/*/ 2>/dev/null | wc -l | tr -d ' ')

# MCP scan — trust `claude mcp list` first, fall back to jq-parsing the two
# canonical config files. Never grep raw JSON for "command" (that counts hooks
# as MCPs) and never reference $(pwd) paths (cwd at /recursive invocation time
# is arbitrary and unrelated to the user's config).
SCAN_MCPS_COUNT=$(claude mcp list 2>/dev/null | grep -cE ' - [✓!✗]' || echo 0)
if [ "$SCAN_MCPS_COUNT" = "0" ] && command -v jq >/dev/null 2>&1; then
  SCAN_MCPS_COUNT=$(
    { jq -r '(.mcpServers // {}) | keys[]' ~/.claude.json 2>/dev/null
      jq -r '(.mcpServers // {}) | keys[]' ~/.claude/settings.json 2>/dev/null
    } | sort -u | grep -c . || echo 0
  )
fi
# If both paths failed (claude CLI missing AND jq missing), leave as "?" and
# let the one-line reaction acknowledge it honestly.
[ -z "$SCAN_MCPS_COUNT" ] && SCAN_MCPS_COUNT="?"

SCAN_MEMORY_FILES=$(ls ~/.claude/projects/*/memory/*.md 2>/dev/null | wc -l | tr -d ' ')
SCAN_PROJECTS_COUNT=$(ls -d ~/.claude/projects/*/ 2>/dev/null | wc -l | tr -d ' ')
```

Store as `SCAN_*` variables. If `~/.claude/` doesn't exist, set all to 0.

**Present the preview to the user immediately.** This is the first delight moment — they should see something specific about themselves before the questions start. Output exactly:

```
Scanned. Here's what's in ~/.claude/:

  CLAUDE.md          {SCAN_CLAUDEMD_LINES} lines
  Custom commands    {SCAN_COMMANDS_COUNT}
  Custom skills      {SCAN_SKILLS_COUNT}
  MCP servers        {SCAN_MCPS_COUNT}
  Active projects    {SCAN_PROJECTS_COUNT}
  Memory files       {SCAN_MEMORY_FILES}

{one-line reaction — see below}

Now four quick questions to fill in the rest.
```

**One-line reaction** — pick one based on what stood out, in the brand voice:

- Heavy setup: `"That's a serious cockpit."`
- Mostly commands: `"Lots of commands. You like leverage."`
- Mostly skills: `"Skills-heavy. Builder-coded."`
- MCP-heavy: `"Wired in. We see you."`
- Long CLAUDE.md: `"That CLAUDE.md is doing work."`
- Memory active: `"You don't forget. Useful."`
- Many projects: `"How many things are you building right now."`
- Light footprint: `"Lean setup. Either you're early, or you don't waste keystrokes."`
- Empty: `"Nothing in ~/.claude/ yet. We'll work with what you tell us."`
- MCP count unknown (`?`): `"MCP scan couldn't read your config — the cockpit is a mystery. Tell us in Q4."`

Don't fabricate. If counts are all low/zero, say so honestly.

**Save for the card (silent):** the 6 scan numbers will appear visually on the final card as the "Stack signature" — a compact 6-cell grid that doubles as the fingerprint behind the Recursive ID. See Step 11 for layout.

Also pick **one optional caption** (≤ 60 chars) to print under the signature, in brand voice. Examples:
- High commands: `"{N} custom slash commands. Loud and clear."`
- High skills: `"{N} custom skills shipped. Builder-coded."`
- High MCPs: `"{N} MCP servers wired in. The cockpit's busy."`
- Active memory: `"Auto-memory across {N} projects. You don't forget."`
- Long CLAUDE.md: `"{N}-line CLAUDE.md. Have you reread it lately?"`
- Many projects: `"{N} projects in flight. Are you okay?"`
- Combine when both fit: `"{N} commands, {M} MCPs. The cockpit is loud."`

Caption rule: only include if at least one signal ≥ 3. Otherwise show the signature grid alone (numbers can speak for themselves).

**Soft cross-check (mental note for the admin, not shown to user):** if Q3 ≥ 4 ("we ship custom agents/MCP servers monthly") but `SCAN_MCPS_COUNT == 0` and `SCAN_SKILLS_COUNT == 0` and `SCAN_COMMANDS_COUNT < 3`, the answers and the local footprint don't quite line up. Don't flag this to the user. Don't change the score. The admin will see it.

---

### Step 2: Q1 — Automation depth

Ask exactly:

```
Q1 of 4 — Automation depth

How does your org build software today?

  1.  Engineers write the code; AI assists with autocomplete and snippets
  2.  AI drafts portions; engineers refine and integrate
  3.  AI drafts whole features; engineers review and edit before merging
  4.  Agents handle most implementation; engineers review PRs and steer
  5.  Infrastructure writes the software. Work runs from Slack, Linear,
      cron, webhooks, customer events.

Reply with a number 1–5.
```

Store as `Q1` (integer 1–5). If the response is empty, out of range, or unparseable, re-ask once. Don't ask for justification.

---

### Step 3: Q2 — Org-wide contribution

Ask exactly:

```
Q2 of 4 — Org-wide contribution

Who in your organization actually contributes to the product with AI?

  1.  Just engineers, and only via AI-assisted coding
  2.  Engineers use AI deeply; other functions have ChatGPT but stay in
      their lane
  3.  Cross-functional use of shared AI workflows — PMs draft PRDs, sales
      drafts outreach, ops automates with scripts, each in their domain
  4.  Non-engineers actively contribute to product and code — sales builds
      prototypes, marketing ships microsites, support writes its own tools
  5.  Anyone in the org can prototype, ship, and automate. Engineering is
      no longer the bottleneck for any function — the org builds together

Reply with a number 1–5.
```

Store as `Q2` (integer 1–5). If the response is empty, out of range, or unparseable, re-ask once.

---

### Step 4: Q3 — Build posture

Ask exactly:

```
Q3 of 4 — Build posture

How do you relate to your stack?

  1.  Off-the-shelf only — we use tools as designed
  2.  Heavy configurator — custom prompts, workflows, deep integrations
  3.  We write small scripts and automations that wire tools together
  4.  We ship custom agents, skills, or MCP servers monthly
  5.  Our stack is mostly things we built; tools are raw material

Reply with a number 1–5.
```

Store as `Q3` (integer 1–5). If the response is empty, out of range, or unparseable, re-ask once.

---

### Step 5: Q4 — What you're building (open, voice-friendly)

Ask exactly:

```
Q4 of 4 — Walk us through it

Tell us about your current setup. What's wired to what? What's the part
you're most excited about right now?

Tip: hit dictate (Whisper Flow, Spokenly, your OS voice input) and just
talk for 60 seconds. Ramble is fine. We'll clean it up for the card.
```

Wait for response. Accept any length — short typed answer or long voice transcript. Both are valid. If the response is empty or just punctuation, re-ask once.

Store raw response as `Q4_raw`.

---

### Step 6: Distill Q4 and confirm

Process `Q4_raw` into two outputs:

- **`building_bullets`**: 2–3 crisp bullets under "What they're building" — name the actual tools, workflows, and connections they mentioned. Preserve their voice. Never invent details they didn't say. If they only gave a one-liner, output one bullet, not three.
- **`excited_about`**: one short sentence (≤ 20 words) capturing what lit them up. Use their words where possible.

Rules:
- Drop filler ("um", "like", "you know", "so basically")
- Keep proper nouns (tool names, company names) exactly as said — flag them in the security loop (Step 10) so user can redact
- Don't add adjectives they didn't use
- If the dictation is incoherent, say so honestly: ask one clarifying question

**Confirm the distillation with the user before it goes on the draft card.** The redact loop in Step 10 catches "remove my company name" but not "you misread what I said" — so surface the summary here while it's still cheap to fix.

Output:

```
Here's what I caught:

  What you're building
  • {bullet 1}
  • {bullet 2}
  • {bullet 3}

  Most excited about
  → {excited_about}

Nail it, or tell me what I got wrong.
```

Accept affirmatives ("nailed it" / "good" / "close enough" / "ship" / "yes" / "correct"): lock in and proceed to Step 7.

Accept corrections ("actually it's X" / "drop the second bullet" / "you missed Y" / "that's not quite it — it's..."): revise the bullets and `excited_about`, re-show the "Here's what I caught:" block, ask again. One correction loop max; after that, proceed with the latest version.

---

### Step 7: Score (silent)

Three numeric dimensions, each 0–100:

- **Depth** = `Q1 × 20`
- **Reach** = `Q2 × 20`
- **Build** = `Q3 × 20`

Total = `Q1 + Q2 + Q3` (max 15).

---

### Step 8: Pick archetype (silent)

Walk the ladder in order. First match wins.

1. **The Conductor** — `Q1 ≥ 4 AND Q2 ≥ 4 AND Q3 ≥ 4`
   > Firing on every cylinder. Personal depth, org adoption, custom builds. The rare one who leads on all three. We're impressed and slightly suspicious.

2. **The Architect** — `Q3 ≥ 4` (and not Conductor)
   > You don't use tools — you treat them as raw material. Custom agents, MCP servers, infrastructure that writes infrastructure. The cockpit is gorgeous.

3. **The Operator** — `Q2 ≥ 4` (and not above)
   > AI scaled across the org. PRDs draft themselves, candidates get screened, reviews happen overnight. You moved a whole company up the curve. That's rare.

4. **The Tinkerer** — `Q1 ≥ 4` (and not above)
   > Deep personal stack, narrow blast radius. Your own workflow is humming; the team hasn't caught up yet. The next move is obvious.

5. **The Apprentice** — `total ≤ 6` (passes gate, low everywhere)
   > Early, but you showed up on the right night. The instinct to run /recursive is the signal. Build one thing this week that builds the next thing. Forward this to whoever showed you.

6. **The Pragmatist** — fallback, everything else
   > Solid across the board. No extreme. You ship, you scale, you don't fetishize the stack. The kind of builder everyone actually wants in the group chat.

---

### Step 9: Generate Recursive ID (silent)

Format: `ARCHETYPE-TOTAL-HASH4`

- **ARCHETYPE**: Uppercase short name (CONDUCTOR, ARCHITECT, OPERATOR, TINKERER, APPRENTICE, PRAGMATIST)
- **TOTAL**: `Q1 + Q2 + Q3`
- **HASH4**: 4-character hex vanity suffix. Derived from local scan data so the ID has texture:
  ```bash
  echo "${SCAN_CLAUDEMD_LINES}-${SCAN_COMMANDS_COUNT}-${SCAN_SKILLS_COUNT}-${SCAN_MCPS_COUNT}-${SCAN_MEMORY_FILES}-${SCAN_PROJECTS_COUNT}-$(whoami)" | shasum | cut -c1-4 | tr 'a-z' 'A-Z'
  ```
  The hash changes when the user's setup changes (CLAUDE.md edits, new projects, etc.), so don't promise stability and don't treat it as an anti-abuse token. Applicant review happens on the admin side. The suffix is aesthetic — it makes the ID look like a real identifier, nothing more.

Examples: `ARCHITECT-12-9F2A`, `OPERATOR-11-B83C`, `APPRENTICE-5-A41E`

---

### Step 10: Security loop (CRITICAL — do not skip)

Render the **draft card** (see Step 11 template) but with a `[DRAFT — REVIEW BEFORE SHARING]` banner above it. Then output exactly:

```
Here's the draft of your card.

If you decide to apply for the Recursive community, the next step is:
screenshot this card and submit it (along with your LinkedIn and WhatsApp
number) so we can review and add you to the group.

Before you do anything with it — read it once and tell me what to redact.

Common things people edit:
  • company names or internal tool names
  • headcount or revenue specifics
  • anything that names a person
  • the "what you're building" bullets if they're too revealing

Reply "ship it" to lock the card as-is, or tell me what to change.
```

Loop: accept edits, regenerate the draft, ask again. Only proceed to Step 11 on explicit "ship it" (or equivalent — "good", "looks good", "go", "lock it").

---

### Step 11: Final card + join footer

Output ONE message containing the final card. No banner. Add the join footer at the bottom. The output IS the brand — it should look great in a screenshot.

Card template (fill in real values):

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                                                                              │
│                            R E C U R S I V E                                 │
│                                                                              │
│       a community for experienced leaders shaping AI-native                  │
│             organizations, one commit at a time                              │
│                                                                              │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│                              {ARCHETYPE_NAME}                                │
│                                                                              │
│                          {one-line verdict here}                             │
│                                                                              │
│                                                                              │
│   Depth     {bar}  {score}                                                   │
│   Reach     {bar}  {score}                                                   │
│   Build     {bar}  {score}                                                   │
│                                                                              │
│                                                                              │
│   What you're building                                                       │
│   • {bullet 1}                                                               │
│   • {bullet 2}                                                               │
│   • {bullet 3}                                                               │
│                                                                              │
│   Most excited about                                                         │
│   → {one-line excited_about}                                                 │
│                                                                              │
│   Stack signature                            ~/.claude/                      │
│   ┌──────┬──────┬──────┬──────┬──────┬──────┐                                │
│   │ {N1} │ {N2} │ {N3} │ {N4} │ {N5} │ {N6} │                                │
│   │  md  │ cmds │ skls │ mcps │ prjs │ mems │                                │
│   └──────┴──────┴──────┴──────┴──────┴──────┘                                │
│   {optional one-line caption — omit if no signal ≥ 3}                        │
│                                                                              │
│   Recursive ID: {ARCHETYPE-TOTAL-HASH4}                                      │
│                                                  recursive {VERSION}         │
└──────────────────────────────────────────────────────────────────────────────┘
```

Substitute `{VERSION}` with the value declared at the top of this skill file (currently `v1.1.0`). Right-align the version line; it's intentionally small so it doesn't compete with the ID.

Bar rendering (20 chars wide, monospace):
- 0   → `░░░░░░░░░░░░░░░░░░░░`
- 20  → `▓▓▓▓░░░░░░░░░░░░░░░░`
- 40  → `▓▓▓▓▓▓▓▓░░░░░░░░░░░░`
- 60  → `▓▓▓▓▓▓▓▓▓▓▓▓░░░░░░░░`
- 80  → `▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓░░░░`
- 100 → `▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓`

After the card, add (outside the box, plain text):

```
─────────────────────────────────────────────────────────────────────────
JOIN RECURSIVE

A small, curated WhatsApp group for experienced leaders shaping
AI-native organizations one commit at a time. No lurkers. Every member ships.

To apply:
  1. Screenshot the card above (Recursive ID = your ticket)
  2. Scan the QR code below — you'll be asked for the screenshot,
     your LinkedIn, and your WhatsApp number (with country code)
  3. We review every applicant: hands-on technical, product,
     and AI leads only
  4. If you're in, we add you to the group and introduce you
─────────────────────────────────────────────────────────────────────────
```

Then output the apply block — hyperlink first (clickable in most terminals, primary CTA for same-device applicants), QR below as scannable ornament for other-device applicants and Twitter-style screenshots. Output the block below verbatim. Do NOT run `qrencode` at runtime; it's already done.

```
Apply: https://tally.so/r/1A2xb1

or scan from another device:

█████████████████████████████
██ ▄▄▄▄▄ █   █▄▄▀▄ █ ▄▄▄▄▄ ██
██ █   █ █ ▀▄ █▀██▀█ █   █ ██
██ █▄▄▄█ █▀██▀▀█▄▄▀█ █▄▄▄█ ██
██▄▄▄▄▄▄▄█▄▀▄█ █ █▄█▄▄▄▄▄▄▄██
██▄ █▀ ▄▄█▀▀▀▄▀▀▄ ▀██▄▀ ▄ ▄██
██ ▀ █ ▀▄ ▄█▀ ▄█▀▄▀▄█▀█▄ ▀███
██  █▄▀█▄▀▄▀▄█▄ ▀▀ ▄▄█▀▄▄ ▄██
████▄▄▀█▄▀ ▀▄█▀█▀▄▄▀▄  ▄ ▀███
██▄▄█▄██▄█▀▀▄▄ ▀▄▄ ▄▄▄ ▄▄████
██ ▄▄▄▄▄ █▀▀  ▄▄█▀ █▄█ ▀▀████
██ █   █ █▄▄▄▀▀██▀▄   ▄▄ ▀▀██
██ █▄▄▄█ █▀▀▀▄███▄█▄ ▀▀ ▀ ███
██▄▄▄▄▄▄▄█▄███▄██▄██▄███▄▄▄██
█████████████████████████████
```

The apply line sits directly under the join footer so the URL is the first thing a same-device applicant sees. The QR is captured in the same screenshot as the card for Twitter-style sharing. The form at `https://tally.so/r/1A2xb1` collects: screenshot upload, Recursive ID, LinkedIn URL, WhatsApp number with country code. Admin reviews → adds to WhatsApp → intros.

> **Maintainer setup (done — update only if the form URL ever changes):**
>
> Current form URL: `https://tally.so/r/1A2xb1`
>
> To regenerate the QR after a URL change:
> ```
> brew install qrencode
> qrencode -t UTF8 -m 2 "https://your-new-form-url" > /tmp/qr.txt
> cat /tmp/qr.txt
> ```
>
> Then replace the QR block above and both form-URL mentions (this file + README if present) with the new values. UTF8 (no ANSI) renders cleanly on light and dark terminal themes both.
>
> Every user who runs `/recursive` gets the same baked-in QR — no `qrencode` install needed on their machine.

---

### Tone references (use sparingly, don't force)

Good lines for fun-finds and verdicts:
- "We're impressed. Also: are you okay?"
- "The cockpit is gorgeous."
- "Base case: ship."
- "You're not scaling headcount, you're scaling the recursion depth."
- "Depth > breadth."

Bad (do not use):
- Anything corporate ("optimize", "leverage synergies", "transform")
- Anything that sounds like LinkedIn
- Generic AI brain emoji energy
- Sparkles, fire emojis, any emoji really
