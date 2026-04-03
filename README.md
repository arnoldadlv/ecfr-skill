# ecfr

A Claude Code skill for looking up, searching, and reading federal regulations from the Electronic Code of Federal Regulations (eCFR).

## What it does

Gives Claude the ability to search, browse, and read CFR text directly from the terminal using the [`@poamslayer/ecfr`](https://www.npmjs.com/package/@poamslayer/ecfr) CLI. Useful for compliance work, defense contracting, CMMC, FAR/DFARS research, and anything that requires reading regulatory text.

## Prerequisites

Install the eCFR CLI globally:

```bash
npm i -g @poamslayer/ecfr
```

No authentication required -- it wraps the public eCFR API.

## Install the skill

```bash
# Clone into your Claude Code skills directory
git clone https://github.com/arnolddelavega/ecfr-skill.git ~/.claude/skills/ecfr
```

That's it. Claude Code picks up skills from `~/.claude/skills/` automatically.

## What Claude can do with this

- **Read regulation text** -- specific sections, parts, or full titles (current or historical)
- **Search the CFR** -- full-text search with title/agency filters
- **Browse structure** -- navigate the hierarchy of any CFR title
- **Track changes** -- see amendments to specific parts/sections since a given date
- **View corrections** -- find FR corrections for any title

## Example prompts

- "What does 32 CFR Part 2002 say about CUI marking?"
- "Search DFARS for cybersecurity incident reporting requirements"
- "Has 48 CFR Part 252 changed since January 2025?"
- "Which agencies own CFR Title 32?"

## License

MIT
