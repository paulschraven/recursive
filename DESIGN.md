# Recursive — Design System

One-page source of truth for the Recursive brand. Extracted from `recursive.md` during [plan-design-review](https://github.com/paulschraven/recursive) on 2026-04-20. Expand as new surfaces ship.

## Core idea

The brand IS the mark of hands-on technical taste. Monospace, lowercase, generous whitespace, zero ornament. If it looks like a SaaS landing page, it's wrong.

## Voice

> Nerdy-confident. Inside-baseball but elegant. The smartest builder at the party who's also the funniest. Genuinely impressed by what people built and slightly concerned about the pace. Affectionate roasting. Closer to math/CS aesthetic than startup-bro. Zero corporate.

**Do:**
- Name specifics. Real tool names, real counts, real commands.
- Direct judgments. "Deep personal stack" beats "impressive personal configuration."
- Punchy standalone sentences. "Depth > breadth." "Base case: ship."
- Affectionate roasting of scale. "We're impressed. Also: are you okay?"
- Parentheticals, one-sentence paragraphs, typing-fast energy.

**Don't:**
- Corporate language (optimize, leverage, synergy, transform).
- LinkedIn tone (excited to announce, thrilled to share).
- Generic AI brain-emoji / rocket / sparkles energy.
- Any emoji, ever.
- Founder-pitch voice. This is not a company pitch, it's a note between peers.

## Color

Two colors. That's the system.

| Token | Hex | Usage |
|-------|-----|-------|
| `--ink` | `#0e0e0e` | Background, all surfaces |
| `--paper` | `#f2efe8` | All foreground: text, rules, logo strokes |

No accent color. No semantic palette. Contrast is the contrast.

If a future surface genuinely needs a third value (e.g., disabled state), derive it by reducing `--paper` alpha. Never introduce new hues.

## Typography

One monospace stack for everything. No sans-serif body copy. If a section feels like it needs a humanist typeface to land, the section is wrong, not the type.

```css
font-family: ui-monospace, "JetBrains Mono", "IBM Plex Mono", Menlo, monospace;
```

**Weights:**
- `500` — wordmark, headings
- `400` — body

**Scale** (modular, base 16):
- Wordmark / hero: `56-72px`
- Section headings: `20-24px`
- Body: `16-18px`
- Meta / captions: `13-14px`

Never go below 16px on body text. Never use `system-ui` as the primary font — it signals "gave up on typography."

**Letter-spacing:** `+2` on the wordmark, `0` everywhere else.

**Line-height:** `1.5-1.6` on body, `1.1-1.2` on wordmark and large headings.

## Logo

Lockup at [assets/recursive-logo.svg](assets/recursive-logo.svg).

**Construction:**
- Icon: circular arrow (ouroboros variant) looping back into its own tail. 14px stroke. The function that calls itself.
- Wordmark: `recursive` in lowercase monospace, 56px, weight 500, +2 letter-spacing.
- Icon sits above wordmark. Both in `--paper`, on `--ink` background.

**Clearspace:** on any side, ≥ 0.75× the height of the wordmark. Don't crowd it.

**Minimum size:** 120px wide. Below that, render icon only.

**Background treatment:** the logo SVG ships with a `#0e0e0e` rect background, making it a self-contained dark badge that reads as intentional on both GitHub light and dark themes. If a future context demands transparent background (e.g., print on colored stock), strip the rect and render the strokes on any `--ink`-equivalent surface.

**Never:**
- Recolor the wordmark outside `--paper` on `--ink`.
- Use a different typeface for the wordmark.
- Add a tagline underline or subline inside the lockup.
- Combine with emoji or icons other than the ouroboros itself.

## Layout principles

- **Monospace grid.** Everything aligns to character width, not pixel columns.
- **Generous whitespace.** Air is the design. If a section feels tight, it is.
- **One job per section.** No dual-purpose sections, no split-screen combos.
- **No decoration.** No blobs, no gradients, no ornamental lines, no icons-in-circles. If an element isn't earning its pixels, delete it.
- **Cards only when card IS the interaction.** Not for layout. Not for decoration.

## AI-slop blacklist (enforce on every new surface)

Any of these is an automatic fail:

1. Purple / violet / indigo gradients. Any gradient, honestly.
2. 3-column feature grid: icon-in-colored-circle + bold title + 2-line description, repeated 3x symmetrically.
3. Icons in colored circles as section decoration.
4. Centered everything (`text-align: center` on headings + descriptions + cards).
5. Uniform bubbly border-radius on every element.
6. Decorative blobs, floating circles, wavy SVG dividers.
7. Emoji anywhere: headings, bullets, hero, footer, Twitter card.
8. Colored left-borders on cards.
9. Generic hero copy ("Welcome to Recursive", "Unlock the power of…", "Your all-in-one solution for…").
10. Cookie-cutter section rhythm (hero → 3 features → testimonials → pricing → CTA).
11. `system-ui` or `-apple-system` as the primary display/body font.

Source: [OpenAI "Designing Delightful Frontends with GPT-5.4"](https://developers.openai.com/blog/designing-delightful-frontends-with-gpt-5-4) + gstack design methodology.

## Accessibility baseline (all surfaces)

- Body text ≥16px.
- Contrast ≥4.5:1 on all text that isn't decorative. The card art (`<pre>` ASCII) has an `aria-label` summary; screen readers read the summary, not the boxes.
- Touch targets ≥44×44px on interactive elements.
- Keyboard nav: all actions reachable, visible focus states.
- Visited vs unvisited link distinction preserved (different color).
- Labels never live inside form fields as placeholders only. Labels are visible when the field has content.
- No auto-play motion. Respect `prefers-reduced-motion`.

## The terminal card (the primary artifact)

The card rendered by `/recursive` in Step 11 of recursive.md is the screenshotable invite. Full layout spec lives in recursive.md:308-346. Notes for any reproduction:

- Exactly 80 columns wide.
- Unicode box-drawing chars (`┌`, `─`, `│`, `┘`) — no ASCII fallback (pipes and dashes). If the terminal doesn't support Unicode, that user isn't the ICP anyway.
- Bar rendering uses `░` and `▓` at 20-char width. See recursive.md:348-354.
- Wordmark `R E C U R S I V E` with spaces between letters — this is the card's version of the logo, not a typo.
- Stack signature 6-cell grid is the visual fingerprint. Keep the counts monospace-aligned.

## Surfaces and their constraints

| Surface | Status | Rules |
|---------|--------|-------|
| Terminal card | Shipped | Full spec in recursive.md:308-346. Don't drift. |
| README | Shipped | Logo at top, install snippet prominent. No marketing blocks. |
| /apply form (Tally) | Next | Mobile-first. Wordmark top. One-line value prop. Three fields. Utilitarian success page. |
| Rejection email | Next | 2-3 sentences. Named rejection. Re-apply in 90 days. No auto-reply on submit. |
| `recursive.xyz/stack/<id>` (Approach B) | Deferred | Render ASCII card inline as `<pre>`. Wordmark above. Full DESIGN.md applies. |

## Deferred decisions (expand DESIGN.md when they arrive)

- Motion system (if/when Approach B adds any)
- Illustration / data-viz style (beyond the ASCII bars — don't add until needed)
- Email template styling beyond plain text
- Swag / physical brand (stickers, etc.)
