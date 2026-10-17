# Sarathi: design note

Sarathi is a private, local-first AI board for career, home and family. It runs on one laptop. This repository is the design note only. The code is private, and so is every piece of personal data.

## The problem

An assistant that handles mail, a calendar and job applications sees everything personal. A cloud assistant sends that data off the machine. The board had to be local, permissioned and testable.

## How it is built

- **Front end:** React 19, with a three.js avatar.
- **Back end:** FastAPI, one process, on 127.0.0.1 only.
- **Agents:** local LLM agents run on a schedule and share one GPU by lease. The model is qwen3 on Ollama, on an RTX 3060 Laptop GPU with 6 GB.
- **Gateway:** the only path to the network. It allows fixed hosts only and logs every request.

## Mitra, the voice assistant

- **Languages:** English, Hindi and Gujarati.
- **Speech in:** Whisper large-v3-turbo; IndicConformer for Hindi and Gujarati.
- **Thinking:** qwen3:4b-instruct on Ollama, on the laptop GPU.
- **Speech out:** AI4Bharat Indian voices.

Measured:
- First audio of a short reply: 1.8 s → 0.3 s after moving the voice to the GPU (median of 3 runs).
- First sentence, timed live: 0.7–1.1 s.
- Generation: 42–44 tokens per second on the 6 GB laptop GPU.
- Scenes acted out from conversation: 0 false triggers in 22 held-out chats; 38 of 42 asks caught.

## The apply agent

Job post → local LLM → tailored resume and cover letter → claim check → ATS quality gate.

A document fails if any claim does not trace to the master resume. The agent never presses Submit; a person does.

## Privacy by design

- All data lives in one folder, outside the code.
- A PII screen runs before every outside look-up.
- The demo board runs on invented data, and a privacy-wall test checks that.
- Agents act on standing permissions: read, change, send. An irreversible action, such as submit, is never granted.

## How it is checked

- 3,225 automated tests as of 17 Oct 2026; all 3,217 passed in the full run of 12 Oct. 20 agents: 17 run on a schedule or a trigger, 3 on a person's turn.
- Mitra's blind pass rate: 47.7% → 77.3%, first vs latest round, each round judged on a fresh set of 44 asks written by a separate agent. Fixes are made on the training set; each blind set is new.
- Changes are scored on held-out sets they were never tuned on.
- AI coding agents write the code to my specs; the tests and the held-out checks decide what ships.

## My part

The architecture, the agents, the claim and privacy rules, and the held-out checks.

More: https://kamal-mehta.com/?r=github#/p/local-ai-board · kamalmehta.ai@gmail.com

## License

This design note is © 2026 Kamal Mehta, licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). You may share and adapt it with credit to Kamal Mehta. The full text is in [LICENSE](LICENSE).
