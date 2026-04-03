---
name: ecfr
description: Look up, search, browse, and read federal regulations from the Code of Federal Regulations (CFR) using the ecfr CLI. Use when the user asks about federal regulations, CFR text, regulatory requirements, compliance rules, DFARS, NIST references in the CFR, CUI rules, acquisition regulations (FAR/DFARS), or needs to check what a specific CFR section says. Also use when tasks require reading regulatory text to answer compliance questions, verify regulatory citations, track regulation changes, or find which agencies own which CFR titles.
---

# eCFR CLI

Search, browse, and read the Electronic Code of Federal Regulations from the terminal. Globally installed via `npm i -g @poamslayer/ecfr`. No authentication required — wraps the public eCFR API.

## Commands

### List all CFR titles

```bash
ecfr titles
ecfr titles --json
```

Returns all 50 CFR titles with title number, name, last amended date, and currency date.

### List agencies

```bash
ecfr agencies                        # all agencies
ecfr agencies --filter defense       # filter by name/short name
ecfr agencies --filter homeland
```

Returns agency name, short name, slug (used for filtering in other commands), and which CFR titles they own.

### Browse title structure

```bash
ecfr structure <title>               # current structure
ecfr structure 32 --date 2025-01-01  # historical structure
```

Returns the full hierarchy tree (chapters, subchapters, parts, subparts, sections) for a CFR title. Use to discover part/section numbers before reading text.

### Read regulation text

```bash
ecfr read <title> --part <n>                    # read an entire part
ecfr read <title> --part <n> --section <n>      # read a specific section
ecfr read 32 --part 2002                        # 32 CFR Part 2002 (CUI)
ecfr read 48 --part 252 --date 2025-06-01       # historical version
ecfr read 32 --part 2002 --xml                  # raw XML output
```

Fetches and renders regulation text. Default output is parsed plaintext; use `--xml` for the raw XML. Use `--date` to read a historical version.

### Search regulations

```bash
ecfr search "<query>"                           # search all CFR text
ecfr search "controlled unclassified" --title 32
ecfr search "cybersecurity" --agency defense-department
ecfr search "CUI" --page 2 --per-page 5
```

Full-text search across the CFR. Returns matching sections with hierarchy, text excerpts, relevance scores, and modification dates.

### Search result counts

```bash
ecfr counts "<query>"                           # counts by title/chapter/part
ecfr counts "cybersecurity" --agency defense-department
```

Returns a hierarchical breakdown of how many results exist per title, chapter, and part — useful for scoping before a full search.

### Track changes

```bash
ecfr changes <title>                            # all changes for a title
ecfr changes 32 --since 2025-01-01              # changes after a date
ecfr changes 48 --part 252                      # changes to a specific part
ecfr changes 32 --part 2002 --section 2002.14
```

Returns amendment history: date, amendment date, name, part, and whether the change was substantive.

### View corrections

```bash
ecfr corrections                                # all corrections
ecfr corrections --title 32                     # corrections for a title
```

Returns FR citations, CFR references, corrective actions, and correction dates.

## Output Format

| Context | Output |
|---------|--------|
| Terminal (TTY) | Colored tables and formatted text |
| Piped (`| jq`) | JSON |
| `--json` flag | JSON (always) |

When processing output programmatically, always use `--json` or pipe to ensure JSON. Parse with `jq` for field extraction.

## Key CFR Titles for CMMC/Defense Work

| Title | Name | Common Use |
|-------|------|------------|
| 32 | National Defense | NIST 800-171 references, CUI rules (Part 2002) |
| 48 | Federal Acquisition Regulations (FAR) | FAR/DFARS clauses (Part 252 = DFARS) |
| 15 | Commerce and Foreign Trade | Export controls, EAR |
| 22 | Foreign Relations | ITAR (Parts 120-130) |

## Common Workflows

**Find what a CFR section says:**
1. `ecfr structure <title>` to discover parts/sections
2. `ecfr read <title> --part <n> --section <n>` to read the text

**Search for a regulatory topic:**
1. `ecfr counts "<query>"` to see where matches cluster
2. `ecfr search "<query>" --title <n>` to get excerpts from a specific title

**Check if a regulation changed recently:**
1. `ecfr changes <title> --part <n> --since <date>` to see amendments

## Gotchas

- The `--json` flag is a **global option** — place it before the subcommand or use pipes: `ecfr --json titles` or `ecfr titles | cat`
- Date format is always `YYYY-MM-DD`
- Agency slugs (for `--agency`) come from `ecfr agencies` — use the slug field, not the name
- The `read` command fetches XML from the eCFR API and parses it — large parts (like all of FAR Title 48) can be slow and produce massive output. Always scope with `--part` and ideally `--section`
- The `search` and `structure` commands hit the eCFR API which may return errors during maintenance windows — retry once if a 400/404 occurs
- Section numbers in `--section` use the full dotted notation (e.g., `2002.14`, not just `14`)