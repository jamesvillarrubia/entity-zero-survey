# Entity Zero — G1 Pilot Survey

**Version:** g1-2026-04-30
**Format:** Pairwise ipsative tone comparisons + forced-choice authenticity
**Companion:** `paper/surveyswap-pilot-design-2026-04-30.md`, `paper/findings/g1-pilot-stimuli-2026-04-30.md`

This is a self-contained vanilla-JS survey for Phase 2's G1 human pilot. Forked from `paper/survey/index.html` (Phase 1) to keep Phase 1 data clean.

## What participants do

1. **Consent** + 18+ check
2. **Attention check** (Instructional Manipulation Check — select "March")
3. **Instructions** — explains the rubric and the 4-point ipsative scale
4. **Ipsative section** (20 conversations × 6 pair sliders each = 120 ratings)
   - Each pair compares two of {Energy, Engagement, Warmth, Rumination}
   - 4-point scale: Strongly A / Somewhat A / Somewhat B / Strongly B
5. **FC authenticity section** (5 paired comparisons of V13 vs V42 stimuli)
6. **Demographics** (4 optional questions)
7. **Debrief + submit**

Total: ~125 ratings, ~13–15 minutes.

## Stimulus data

The 30 stimuli (20 ipsative + 5 FC pairs × 2 stimuli) are at `conversations/stimuli.json`. To regenerate:

```sh
python3 paper/code/g1-pilot-stimulus-sampler.py
cp paper/findings/g1-pilot-stimuli-2026-04-30.json study-survey-g1/conversations/stimuli.json
```

## Submission backend

Same Neon Data API as Phase 1 (`paper/survey/neon-schema.sql`). The anonymous JWT has INSERT-only permission via Postgres RLS. Each submission creates one row in the `responses` table with:

- `payload.version = 'g1-2026-04-30'` (filter Phase 1 vs G1 by this field)
- `payload.ipsativeResponses` — 20 trials × 6 pair selections each
- `payload.fcResponses` — 5 forced-choice pair selections
- `payload.imcAnswer`, `payload.imcPassed`
- `payload.demographics`

The `version` field on payload distinguishes G1 from Phase 1.

## Local development

```sh
cd study-survey-g1
python3 -m http.server 8000
# Open http://localhost:8000
```

Or any static-file server. The script needs `fetch('./conversations/stimuli.json')` to work, so `file://` won't load (CORS).

## Deployment

GitHub Pages target: `https://jamesvillarrubia.github.io/entity-zero-survey-g1/` (or similar — pick a URL that doesn't collide with Phase 1 at `entity-zero-survey/`).

Deployment pattern from Phase 1:

```sh
git init
git remote add origin git@github.com:jamesvillarrubia/entity-zero-survey-g1.git
git add . && git commit -m "Deploy G1 pilot v1"
git branch -M main && git push -u origin main
# Then enable GitHub Pages on main branch
```

Or rsync into an existing pages repo:

```sh
rsync -av --delete study-survey-g1/ /path/to/pages-repo/
cd /path/to/pages-repo && git add . && git commit -m "G1 pilot deploy" && git push
```

## SurveySwap listing

Title suggestion: **"Rate AI conversation tone — 13 min, 4-dimension comparison study"**

Description:
```
Help research on emotional tone in AI-generated text-message conversations.
20 short conversations to rate via paired comparisons (e.g., "more warm or
more engaged?"), plus 5 quick A/B comparisons. ~13 minutes. Pays SurveySwap
credits.
```

Estimated time: 13 min. Compensation: SurveySwap credits only (~10-15 per response).

The survey URL accepts `?ss_id=...` and `?ss_return=...` query params for SurveySwap's referral flow.

## Pre-registered analysis

See `paper/surveyswap-pilot-design-2026-04-30.md` and the OSF amendment at `paper/osf-phase2-amendment-draft-2026-04-30.md`.

Primary tests:

- **P1**: ipsative-derived SP × Rumination correlation across 10 Phase-2-style stimuli (predict r ≤ -0.3, matching LLM-rater bipolar pattern)
- **P2**: same on 10 study-runner stimuli (predict r ≥ +0.3, matching unipolar pattern)
- **P3**: cross-set divergence Fisher z > 1.96
- **H1 (architecture)**: V42 selected at rate > 50% on FC authenticity pairs

Exclusion rules (pre-specified):

1. Skipped > 5 of 20 ipsative stimuli → exclude
2. Failed attention check (IMC) → flag (but pay)
3. Total duration < 5 min → exclude
4. Rated all 6 pairs identically on > 70% of stimuli → exclude (non-engagement pattern)
5. Native English = "no" on this English-only survey → exclude
