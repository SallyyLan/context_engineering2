# LLM Agent Instruction Architecture — Campaign Voucher Lookup via Natural Language

> Context engineering for an internal LLM-powered lookup agent. The goal: let campaign operations and marketing teams query voucher configurations instantly — in plain language, without touching a database.

---

## The Problem

Campaign teams needed quick answers about voucher configurations across five recurring campaign types. The pain points:

- **Terminology fragmentation** — the same voucher type had different names across teams, and Chinese/English aliases were used interchangeably
- **Sensitive data exposure risk** — raw quota counts and promotion IDs are confidential; ad-hoc queries had no guardrails
- **Out-of-scope noise** — users often mixed performance questions (GMV, YOY, conversion) with configuration questions, causing query errors or wrong data pulls
- **No single source of truth** — campaign details lived in a database that most team members couldn't query directly

The existing approach required analysts to handle ad-hoc lookup requests manually, one at a time.

---

## The Solution

A structured instruction file that governs how an LLM agent handles natural language voucher lookup — from noisy input to a clean, safe, bilingual output.

The core mechanism is a **five-step workflow**:

```
STEP 1 — Noise Filtering
         Strip out-of-scope terms; extract only valid campaign / voucher keywords

STEP 2 — Keyword Confirmation (mandatory before any data fetch)
         Confirm extracted keywords with the user; adapt to follow-ups

STEP 3 — Terminology Resolution
         Map aliases and natural language to internal campaign codes and field names

STEP 4 — Data Fetch
         Run the correct pre-assembled query; fetch only what was asked

STEP 5 — Answer
         Output a clean summary table with masked sensitive values
```

No step can be skipped. No data is fetched before the user confirms what was understood.

---

## What's in This File

| Section | What It Does |
|---|---|
| Workflow | The five-step execution protocol — noise filtering through answer output |
| Terminology Reference | Alias maps for campaign names, voucher types, and attributes in Chinese and English |
| Allowed Columns & Routing | Which query template to use for each question type; data source restriction |
| Answer Format | Summary table structure; quota masking rules |
| Bilingual Response Rule | How the agent mirrors the user's input language |
| Hard Rules | Non-negotiable constraints covering output format, data exposure, and confirmation |
| Clarification Triggers | When and how the agent asks for more information |

---

## Key Design Decisions

**Noise filtering before extraction.** Users often ask mixed questions — campaign performance alongside voucher config. The agent discards out-of-scope terms before extracting keywords, preventing wrong data pulls without requiring the user to rephrase.

The remaining decisions follow the same principle of protecting both data integrity and user experience:

- **Confirmation before fetch.** The agent always confirms what it understood before querying — and adapts what it confirms based on whether it's a new question, a follow-up, or a correction.
- **Sensitive counts are always masked.** Raw quota and promotion counts are confidential. Masking is a silent system default — three tier labels replace all numeric counts, and the agent never asks permission to apply it.
- **Single approved data source.** The agent is restricted to one table. No joins, no alternative tables, no inferred columns — any request outside this boundary is explicitly declined.
- **Bilingual by default.** The agent mirrors the user's input language — Chinese, English, or mixed — across every part of the response including table headers and confirmation messages.

---

## Project Context

Built as part of an internal AI tooling initiative to reduce analyst dependency on manual lookup requests and ad-hoc SQL. Each design constraint maps to a real operational risk: data exposure, wrong campaign identification, or silent misinterpretation of noisy input.

This repository contains the **sanitized version** of the instruction file with company-specific identifiers removed.
