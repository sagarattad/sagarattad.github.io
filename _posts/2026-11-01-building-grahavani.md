---
title: "Building Grahavani: An Astrology App Where the AI Isn't Allowed to Guess"
description: "How I built a free Vedic astrology app on free tiers, and why every AI reading is checked against the chart before anyone sees it."
categories: [AI, Next.js]
---

Most astrology apps I tried fell into one of two camps. Either they buried a simple Kundli behind ads and paywalls, or they leaned on fear: a scary-sounding dosha, followed by a paid puja to "fix" it. I wanted something calmer. A free app that shows your chart clearly, explains it in plain language, and never tries to sell you anything.

That became **Grahavani**: a mobile-first Vedic astrology (Parashari Jyotish) web app. This post is about how it's built, and about the one rule that shaped almost every decision: *the AI explains, it never calculates.*

## What it does

- **Kundli** with North Indian (diamond) charts for D1 and the D9 Navamsa
- **Yogas and Doshas**, each with a rating and the reasons behind it
- **Vimshottari Dasha**, so you can see which period you're in and what comes next
- **Kundli Milan** (Ashtakoot Gun Milan) for two people, including dosha cancellations
- **Manglik check**
- **AI readings** in plain language, with follow-up questions
- **PDF export** of the results

## Rule one: I don't calculate anything myself

Planetary positions are not something to get "roughly right". A degree off near a sign boundary can change the Lagna, the houses and the whole reading. So Grahavani doesn't compute positions, houses, dasha dates or Gun Milan scores at all. Every number comes from the free [Kundlit](https://kundlit.com) API, which uses Swiss Ephemeris and the Lahiri ayanamsa.

Before writing any code I read Kundlit's API spec and made one live call to each endpoint I needed, because the response shapes weren't documented. I also checked that every planet's house matched the whole-sign formula against the Lagna. All Kundlit code lives in a single file, so if I ever need to switch providers, there's exactly one place to change.

## Rule two: the AI only explains what's already computed

The AI's job is to turn a chart into readable paragraphs: personality, career, marriage, wellbeing, the current dasha. It gets a **facts payload** built from the computed chart, and nothing else:

- Positions, houses, nakshatras, yogas, the current dasha. Never names, places, or birth date and time. The AI doesn't need them, so it never sees them.
- A strict system prompt: use only these facts, never invent a yoga or a date, and say so when something isn't in the data.
- Guardrails on tone. No predictions of death, illness, divorce or "bad luck". Never tell anyone to marry or not marry. Remedies are mentioned only if they're traditional and optional, and paid pujas, gemstones or consultations are never recommended.

> The answer is always structured JSON, one field per card. That makes it easy to stream into the page, and easy to check.
{: .callout .note}

## Trust, but verify: checking every reading

A prompt is a request, not a guarantee. Language models are very good at sounding confident while quietly getting a detail wrong: "Mars in the 7th house" when Mars is in the 8th, or a koot scored 5/8 when the real score is 6/8. In astrology those details are the whole point.

So every AI reading goes through a validator in code *before* it's shown as final or cached. It reads the text, pulls out every factual claim it can recognise, and compares it with the chart:

```typescript
// Simplified: each "<Graha> in <place>" claim must match the chart
for (const match of text.matchAll(PLACEMENT_RE)) {
  const [, graha, rashi, house] = match;
  if (!chart.hasPlacement(graha, rashi ?? house)) {
    problems.push(`${graha} is not in ${rashi ?? `house ${house}`}`);
  }
}
```

The real version also checks koot points against the Gun Milan result, totals out of 36, and dasha lords against the timeline. It knows both the English and Sanskrit names of each graha, so a claim is caught whichever name the model uses. General numbers like "12 houses" or "27 nakshatras" are ignored.

If a reading fails, the model gets **one retry** with a note listing exactly what was wrong. If it fails again, the AI text is dropped and the page simply shows the computed data, which is always correct. While a reading streams in, each card shows a small "checking…" badge until it passes.

> A wrong fact is never shown just because it sounded fluent. If the explanation can't be verified, you get the data without the explanation.
{: .callout .tip}

## Not trusting the API blindly either

Kundlit returns a "strength score" for each planet, and my first plan was to use it for the Yoga and Dosha ratings. Then I compared it with a published Shadbala table for a test chart. The rankings were almost unrelated: a Spearman correlation of **−0.18**.

So I stripped that field out and built Grahavani's own **classical rating**: a transparent, points-based score from dignity, house placement, combustion and planetary age. Every rating shows its reasons, so you can see *why* a yoga is called Strong, Moderate or Weak. It's clearly labelled as Grahavani's own interpretation, and the app never calls it Shadbala, because it isn't.

## Running it on free tiers

The whole app runs on free plans, and OpenAI usage is the only real cost:

| Piece | Choice |
|---|---|
| App | Next.js and TypeScript on Vercel |
| Database | Supabase Postgres with Prisma |
| AI | OpenAI through the Vercel AI SDK, with streamed structured output |
| Bot protection | Cloudflare Turnstile |
| Place search | Open-Meteo geocoding (place to latitude, longitude and timezone) |

Free tiers come with limits, so the design works around them:

- **Everything is cached.** A chart is computed once and stored. A reading is generated only when someone clicks "Get reading", and cached against a hash of the facts.
- **A shared rate limiter** in Postgres keeps calls to Kundlit at about 70% of its per-minute limits, and backs off whenever the API says to wait.
- **A daily job** keeps the free database from going to sleep and deletes anonymous charts nobody has opened in 12 months.
- **Costs are capped twice:** a hard monthly budget in the OpenAI dashboard, and an app-side guard that pauses readings when the month's estimate is reached. A full reading costs about three rupees.

## Privacy by default

Birth details are personal, so the app collects as little as it can. No names go to the AI. Share links are long and unguessable, and every result has a Delete button. IP addresses are stored only as hashes. The app also has a private mode, where only approved Google accounts can sign in, and that's how I run it.

## Built with Claude Code

I built Grahavani with the help of [Claude Code](https://claude.com/claude-code), Anthropic's AI coding assistant. I wrote the plan first: what the app should do, which free services to use, and the rules the AI readings must follow. Then I worked through it phase by phase with Claude Code, reviewing each change and testing it before moving on.

It's a fitting partnership for this project. The app itself is about using AI where it helps while checking its work, and that's exactly how I used AI to build it.

## What I learned

1. **Decide what the AI is not allowed to do.** The most useful part of the prompt is the list of things it must never say.
2. **Validate model output in code.** Prompts reduce mistakes; a validator catches the ones that slip through.
3. **Verify your data sources too.** The planet-strength score looked perfectly reasonable until I compared it with something independent.
4. **Free tiers are enough if you design for them.** Caching and rate limiting were part of the plan from day one, not something bolted on later.
5. **Tests pay for themselves.** More than 200 unit tests, from chart placement to the AI validator, let me rework the whole interpretation layer several times without breaking it.

---

The code is open on [GitHub](https://github.com/sagarattad/grahavani). If you work on astrology software, or on keeping LLMs honest in any domain, I'd love to hear from you.
