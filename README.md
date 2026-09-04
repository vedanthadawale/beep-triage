# Beep Triage

Problem Statement 1, Beep APM assignment.
Live: https://vedanthadawale.github.io/beep-triage/

## The problem

Every incoming ticket has to be read by a person before anyone can act on it. That's a bottleneck, and different people route the same ticket differently. This tool does the routing automatically and shows why.

## How it works

You submit a title, description, your name and an optional type. The tool scores the text against three keyword lists (dev, design, content), then runs four checks:

**G0. Is this a live incident?** If yes, straight to the owning team, PM copied. A P0 shouldn't wait for a prioritisation call.

**G1. Is the outcome specified?** If the ticket contains open questions ("should we", "would be nice", "not sure"), nobody has decided what done means yet. Goes to the PM.

**G2. Does one team own it?** Needs two matching keywords, not one. A single match is a coincidence.

**G3. Any trade-offs?** Words about scope, cost or sequencing mean it's a prioritisation call. Goes to the PM.

Pass all four and it goes to a team. Fail any and it goes to the PM. The panel on the right shows which checks passed and which words triggered them.

## Why I built it this way

**Rules, not AI.** The brief asks for explainable routing. Rules let you point at the exact word that caused a decision. They're also free and instant. The downside is keyword matching is brittle and English-only. An LLM would be useful as a fallback on low-confidence cases, not as a replacement.

**Biased toward the PM.** The two mistakes aren't equally expensive. Send a judgment call to the dev team and it gets silently built or dropped. Send a task to the PM and it costs them thirty seconds. Manual PM triage is what happens today, so falling back to it is free. That's why anything uncertain goes to the PM.

**Keyword lists sit at the top of the file, separate from the logic.** When a ticket routes wrong, a PM adds a word. No engineer needed.

## Assumptions and shortcuts

- Frontend only. No server or database. Runs in one HTML file.
- Tickets don't persist. They reset on refresh. Fine for showing routing, not for a shared workspace (that's PS2).
- No login. Anyone with the link can submit.
- Routed tickets aren't pushed to Jira or Slack yet. The lanes are a display.
- Keyword lists are seeded from Beep's vocabulary and would need tuning against a real backlog.

## Testing

I ran ten tickets covering every path. Three routed wrong and I fixed them:

- "Register button is too light to read" went to the PM. The design list had no words for legibility.
- "Something is off with the app, feels slow and cluttered" passed every check on one weak keyword and landed on design. This was the bad one, since a vague ticket got confidently misfiled. Fixed by requiring two keywords at G2 and adding vagueness words to G1.
- "It should wrap instead of clipping" went to the PM because "instead of" was in the trade-off list. In that sentence it's just spec language. Removed it.

## What I'd improve with more time

Let a PM correct a routing decision in one click and log the correction, then show which words most often precede a correction. Right now the keyword lists only improve if someone remembers to edit them, so they'll drift as the product changes. I'd also connect this to PS2 so a routed ticket lands on the board automatically, and push tickets into whatever the team actually uses.
