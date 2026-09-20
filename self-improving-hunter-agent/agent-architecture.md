# Self-Improving Bounty Hunter Agent — Architecture

**Status:** design doc, 2026-09-20
**Idea in one line:** Ghost Architect loops as the brain, the pqliar CLI as the hands, and a compounding memory (primitive library + reflection log) as the part that gets smarter every hunt.

---

## 1. The three layers

```
┌─────────────────────────────────────────────────┐
│  BRAIN — Ghost Architect loops                  │
│  Rosen maps → Lopez sweeps → Curry investigates  │
│  → DeVoss scales → reflect → repeat              │
└──────────────────────┬──────────────────────────┘
                       │ decides
┌──────────────────────▼──────────────────────────┐
│  HANDS — pqliar CLI                             │
│  plan, setup, dork, wayback, seek, fuzz, loot   │
│  + new: analyze, hunt, reflect, primitive       │
└──────────────────────┬──────────────────────────┘
                       │ reads/writes
┌──────────────────────▼──────────────────────────┐
│  MEMORY — sqlite store                          │
│  primitive library · target memory · sessions   │
│  confidence scores · cross-pollination queue    │
└─────────────────────────────────────────────────┘
```

The brain never touches the network directly. Every action goes through the hands, and every hand passes through the guardrails. The memory is the only thing that persists between hunts, which is what makes the whole thing compound.

---

## 2. New pqliar commands

### `pqliar analyze` (build first — immediate value, zero autonomy risk)
Pipes tool output into an LLM for triage. This is the side thought from 2026-09-20, and it's the right first step because it's useful on day one with no agent loop at all.

```bash
httpx -l subs.txt -json | pqliar analyze --program nba
ffuf -u https://target/FUZZ -w words.txt -json | pqliar analyze --focus "auth weirdness"
cat wayback-urls.txt | pqliar analyze --focus "interesting parameters"
```

What it does:
- Parses the piped input (JSON lines, plain lists, ffuf JSON)
- Sends it to the fast LLM tier with a triage prompt: rank by interestingness, flag anomalies, suggest next probes
- Prints a ranked, human-readable shortlist to the terminal, not a wall of raw output
- Logs the input hash + output summary to the session log (feeds `reflect` later)

### `pqliar hunt <target>` (the loop runner)
Runs the phased Ghost Architect loop against one target. **Human approval gate between every phase.** Not autonomous at first, and that's deliberate.

```
Phase 1 RECON (Rosen)    → map infra graph, fingerprint stack, inventory surface
        [gate: show the map, user approves]
Phase 2 SWEEP (Lopez)    → boring-surface checks across the inventory, log anomalies
        [gate: show anomaly list, user picks threads]
Phase 3 INVESTIGATE (Curry) → curiosity threads on approved anomalies, specialist modules
        [gate: show candidate primitives/findings]
Phase 4 SCALE (DeVoss)   → generate mutations, replay across endpoints, cross-pollinate
        [gate: review before anything beyond read-only]
Phase 5 REFLECT          → auto-draft the reflection template from the session log
```

Each phase is just orchestrated calls to existing pqliar commands plus `analyze` for triage. The loop adds sequencing, memory, and gates. Nothing else.

### `pqliar reflect [--session <id>]`
Generates a filled-in reflection template from the session log: what was checked, anomalies found, threads spawned, primitives discovered, requests made. The human edits, then it files to `reflections/<target>-<date>.md` and updates memory.

### `pqliar primitive <add|list|replay>`
- `add`: catalog a new bypass with target conditions; prompts for the fields in the library schema
- `list`: show library, sortable by confidence score
- `replay --prim PRIM-001 --target <host>`: run a primitive's mutations against a target's endpoints

---

## 3. Memory design

One sqlite database per hunter (not per target): `~/.pqliar/memory.db`. Three tables, matching the Ghost Architect spec:

- **targets** — infra graph, tech fingerprint, behavioral baselines, past sessions (even dry holes). Never start from zero.
- **primitives** — id, category, description, root cause, target conditions (JSON), base payload, mutations (JSON), success/failure history, confidence score, cross-pollination queue. Confidence formula from the spec: `(confirmed × 2) + (potential × 1) − (failures × 0.5)`. Score > 5: suggest auto-replay on matching new targets. Score < −3: archive, don't delete.
- **sessions** — every `hunt` run: phase logs, requests made, anomalies, threads, findings, the filed reflection. This is what `reflect` reads.

Export: `pqliar memory export --format json` dumps the library to JSON for backup and for pushing to the Muse-Agent-Hub repo. The hub holds the curated public copy; the local db holds everything including target-specific notes that shouldn't be public.

---

## 4. Guardrails (load-bearing, not bolted on)

These live in one module that **every** command passes through. No command touches the network without clearing all four:

1. **Scope gate.** Load the program's scope (CSV, like the NBA scopes file) at session start. Any host not explicitly in scope is a hard block, not a warning. Out-of-scope hosts the recon turns up get logged as "observed, not touched."
2. **Throttle.** Per-program requests/second from policy (NBA: 3 r/s). Token bucket in the guardrail module, shared across commands. Aggressive scanning, brute force, and password spraying are refused at the CLI level, not left to the operator's memory.
3. **Read-only default.** Anything state-changing (POST/PUT/DELETE beyond clearly safe endpoints, destructive payloads) requires explicit human approval, every time. The agent proposes; the human disposes.
4. **Audit log.** Every request the agent makes: timestamp, method, host, path, phase, primitive id if any. This is what makes `reflect` honest and what keeps the hunter out of trouble.

The day the gates come off any phase, it should be a deliberate config change with the hunter's name on it, not drift.

---

## 5. LLM wiring

Two tiers, configured by env, no hardcoded providers:

- **Fast tier** (`PQLIAR_FAST_MODEL`): triage in `analyze`, mutation generation, log summarization. High volume, low cost.
- **Reasoning tier** (`PQLIAR_REASON_MODEL`): curiosity-thread investigation, architecture mapping, reflection drafting. Called rarely, at gates.

Calls stream to the terminal UI (the pqliar wishlist item) so the hunter watches the reasoning, not a spinner. Every LLM call logs prompt hash + output summary to the session, never full target data to disk longer than the session needs.

---

## 6. Build order (autonomy last)

1. **`analyze`** — the pipe. Useful immediately, no loop, no risk. Proves the LLM wiring and the terminal UX.
2. **Memory db + `primitive` commands** — the library becomes real and queryable. Manual use first.
3. **`hunt` with gates** — the loop runner, human approves every phase transition. This is the "supervised agent."
4. **`reflect`** — auto-drafted debriefs from session logs. Closes the learning loop: hunt → reflect → library update → smarter hunt.
5. **Gate relaxation** — only after N supervised hunts with clean audit logs: allow phases 1–2 to run ungated, keep 3–5 gated. Full autonomy stays opt-in per program.

Skipping straight to 5 is how you get banned from a program or, worse, touch something out of scope at machine speed.

---

## 7. Monorepo note

This doc is location-agnostic on purpose: hands/brain/memory are modules with clean interfaces, so they land cleanly whether pqliar stays its own repo or everything consolidates into a monorepo later. If the monorepo happens, this becomes `packages/hunter-agent/` with `pqliar` as `packages/pqliar-cli/`, and the hub repo keeps the public, sanitized copies. No rewrite needed either way.

---

## 8. What "killer" looks like

Six months in: you point `pqliar hunt` at a new program, it maps the graph, sweeps the boring surface, and replays every primitive with confidence > 5 that matches the stack, all inside the program's throttle, all logged. You spend your time only on the Curry threads it surfaces and the gates it stops at. Every dry hole still files a reflection, so the library keeps compounding. That's the self-improving part: the agent's tenth hunt starts smarter than its first, and so do you.
