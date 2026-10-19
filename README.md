# Sarathi: design note

Sarathi is a private, local-first AI board for career, home and family: 20 agents and a voice assistant on one 6 GB laptop GPU. This repository is the design note only. The code is private, and so is every piece of personal data.

## My part

Designed the architecture, the agents, the claim and privacy rules, and the held-out checks.

## The problem

An assistant that handles mail, a calendar and job applications sees everything personal. A cloud assistant sends that data off the machine. The board had to be local, permissioned and testable.

## How it is built

The front end is React 19 with a three.js avatar, and the back end is one FastAPI process that listens on 127.0.0.1 only. Twenty local LLM agents (qwen3 on Ollama) share one RTX 3060 Laptop GPU with 6 GB by lease: 17 run on a schedule or a trigger, and 3 on a person's turn. A gateway is the only path to the network; it allows fixed hosts only and logs every request.

## Mitra, the voice assistant

- **Languages:** English, Hindi and Gujarati.
- **Speech in:** Whisper large-v3-turbo; IndicConformer for Hindi and Gujarati.
- **Thinking:** qwen3:4b-instruct on Ollama, on the laptop GPU.
- **Speech out:** AI4Bharat Indian voices.

Measured:
- TTS synthesis latency (three-word reply): 1.8 s → 0.3 s after moving the voice to the GPU (median of 3 runs).
- Time to first audio (first sentence, timed live): 0.7–1.1 s.
- Generation: 42–44 tokens per second on the 6 GB laptop GPU.
- Scenes (physical actions Mitra acts out) triggered from conversation: 0 false positives in 22 held-out chats; recall 38 of 42.

## The apply agent

Job post → local LLM → tailored resume and cover letter → claim check → ATS quality gate.

A document fails if any claim does not trace to the master resume. The agent never presses Submit; a person does.

In use, 9–15 Oct 2026: 17 applications prepared (a tailored resume and cover letter each), 2 submitted, both with my own Submit press. The one form the agent filled with me took 4 minutes.

## Privacy by design

- All data lives in one folder, outside the code.
- A PII screen runs before every outside look-up.
- The demo board runs on invented data, and a privacy-wall test checks that.
- Agents act on standing permissions: read, change, send. An irreversible action, such as submit, is never granted.

## How it is checked

- 3,217 automated tests, all passing in the full run of 12 Oct 2026.
- Mitra's held-out pass rate: 55.3% (mean of rounds 22–24) → 71.2% (mean of rounds 25–27); best round 77.3%, latest 65.9% (44 test prompts a round, about ±8 points per 3-round mean; a prompt passes only if an LLM judge gives every one of 10 rubric items 0.9 or more). Each set is written fresh by a separate agent. Fixes are made on the dev set; each held-out test set is new.
- Changes are scored on held-out sets they were never tuned on.
- Spec-driven development: I design the system and write the specs, AI coding agents implement them, and the tests and held-out evals decide what ships.

## Limits

- Each held-out set has 40–44 test prompts (44 in rounds 22 to 27), so one prompt moves the pass rate by about 2.3 points, and each round's set is new.
- The latency figures are medians of 3 runs on one laptop.

More: https://kamal-mehta.com/?r=github#/p/local-ai-board · kamalmehta.ai@gmail.com

## License

This design note is © 2026 Kamal Mehta, licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). You may share and adapt it with credit to Kamal Mehta. The full text is in [LICENSE](LICENSE).
