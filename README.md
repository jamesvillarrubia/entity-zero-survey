# Mood-Rating Pilot Survey

A short (~12 minute) survey where participants rate fictional text-message conversations on mood dimensions. Used to validate automated rating tools against human judgment.

## For participants

If you received this link from Prolific, just open the URL — your Prolific ID will be picked up automatically and your 12-stimulus stratified sample will be seeded by it. Rate all 12, follow the Prolific return link at the end.

## For researchers / reviewers

Survey source: <https://github.com/jamesvillarrubia/entity-zero-survey> (this repo)
Full paper and code: <https://github.com/jamesvillarrubia/project-zero> (private; access on request to james.villarrubia@gmail.com)

### Regeneration

The deployed stimulus pool (`stimuli.json`) is a public subset of the generated pool from the main repo. It is regenerated via `pnpm tsx src/study/survey-prepare-stimuli.ts` in the main repo. Ground-truth LLM ratings (`lluRatings`) are stripped from this public file to prevent participants from seeing scores they are trying to replicate.

### Technical

- Pure client-side: HTML + CSS + vanilla JS, no build step
- localStorage persistence with content-hash invalidation
- Stratified per-participant sampling seeded by Prolific PID
- Response capture: JSON download (pilot) → server-side POST endpoint (full run)

### Ethics

Self-declared exempt under 45 CFR 46.104(d)(2). No institutional IRB. See the welcome-screen disclosure for participants.

### Contact

- Researcher: james.villarrubia@gmail.com
- Prolific researcher page: (link once study is posted)
