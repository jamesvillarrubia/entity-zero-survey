# G1 Pilot — Deploy Notes

This directory is staged for deployment as a subpath of the existing GitHub Pages repo.

**Pages URL when deployed:** `https://jamesvillarrubia.github.io/entity-zero-survey/g1/`

## Sync to Pages repo

The actual GitHub Pages source is at `https://github.com/jamesvillarrubia/entity-zero-survey`. To deploy this G1 subpath:

```sh
# From the entity-zero repo root:
PAGES_REPO=~/path/to/local/clone/of/entity-zero-survey  # adjust to your local clone

# Sync the G1 subpath contents
mkdir -p $PAGES_REPO/g1
rsync -av --delete deploy/entity-zero-survey/g1/ $PAGES_REPO/g1/

# Commit and push
cd $PAGES_REPO
git add g1/
git commit -m "Deploy G1 pilot survey at /g1/"
git push origin main
```

After ~30s GitHub Pages rebuilds and the survey is live at:
`https://jamesvillarrubia.github.io/entity-zero-survey/g1/`

## Pre-launch checklist

- [ ] Open the deployed URL on desktop and mobile, click through the entire flow once with a test submission
- [ ] Verify a test row appeared in Neon (filter `payload->>'version' = 'g1-2026-04-30'`)
- [ ] Delete the test row before launching: `DELETE FROM responses WHERE source = 'test' AND payload->>'version' = 'g1-2026-04-30'`
- [ ] Create the SurveySwap listing with the URL above
- [ ] Listing description: see README.md in this directory
- [ ] Recruitment target: ~20 raters

## Test mode

To submit a test row that won't pollute analyses, append `?source=test` to the URL:
`https://jamesvillarrubia.github.io/entity-zero-survey/g1/?source=test`

The submission's `source` field will be 'test' and `pilot-analysis` filters those out automatically.

## Why a subpath, not a separate repo?

Same Neon backend, same JWT, same domain (so the JWT's `iss` claim matches without changes), same GitHub repo to maintain. Phase 1 data flows to the same `responses` table; G1 is filterable by `payload.version = 'g1-2026-04-30'`.

Phase 1 listing on SurveySwap continues to point at `/`. The G1 listing points at `/g1/`. Two separate listings; same backend.

## What this directory contains

- `index.html` — the actual survey (vanilla HTML/JS, ~640 lines)
- `conversations/stimuli.json` — 30 stimuli (20 ipsative + 5 FC pairs × 2)
- `README.md` — survey design + analysis plan summary
- `DEPLOY.md` — this file
