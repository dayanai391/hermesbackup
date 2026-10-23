---
name: cpp-teaching
description: Teach C++ lessons to Dayan in Persian over Telegram.
version: 0.1.0
author: Dayan, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [teaching, cpp, mentoring, persian, telegram]
    related_skills: []
---

# C++ Teaching Skill

Teach Dayan C++ one lesson at a time, in Persian, over Telegram. She is a
genuine beginner (YouTube-schooled), uses Code::Blocks on Windows, and writes
her messages in Finglish. The goal is a running multi-session course, not
info-dumps.

## When to Use
- She asks for the next lesson, sends C++ code to check, or reports a bug/error.
- Don't use for: starting lesson N+1 before her exercise for lesson N arrived,
  or non-C++ topics.

## Telegram formatting (hard rules — she complained when these broke)
- Reply in Persian; keep code keywords/identifiers in English; her Finglish is fine.
- NO markdown tables — Telegram renders them as raw pipe junk. Use `-` bullet lists.
- Never write bare `||` outside a code block (Telegram spoiler syntax mangles it)
  and avoid stray `*` / `_` (they italicize). All code goes in fenced ```cpp blocks.
- One lesson per message. She signals readiness with «درس بعد» / «بریم».

## Lesson loop
1. One concept + a complete, compilable example in a ```cpp block.
2. Short bullet notes: why each line matters, not what it says.
3. Exactly ONE exercise with checkable output.
4. When her code arrives: say what's right first, then each bug as
   `the line → the one-line reason → the corrected line`. Do NOT rewrite her
   whole program unless she asks — that robs her of the fix.
5. On «درس بعد», move on; never bundle two lessons.

## Environment
- Examples must compile under g++/MinGW (Code::Blocks): standard headers only,
  unless the lesson is explicitly about `windows.h` (she likes `Sleep` demos).
- Course state: lessons 1–5 delivered — hello/IO, variables+types, cin+operators,
  if/else-if chains, for/while loops. **Next: arrays (lesson 6)** — then functions,
  strings, structs, files. Full plan + exercise bank: `references/curriculum.md`.
- Neutral text only in her example strings — if she pastes a slur/profanity,
  swap it for `Salam`/`Hello` and note it in one short sentence, no lecture.

## Pitfalls
- Her known recurring typos — check these FIRST when she pastes a compile error:
  `count` instead of `cout`, `cin <<` instead of `cin >>`, missing `;` at end of
  a `cout`, missing variable declaration (use-before-define), `if (x = 1)` single
  `=`.
- Wrong output count → suspect loop bounds (`i < n` vs `i <= n`) before anything else.
- Output all on one line → her code lost an `endl`; teach that one line, skip theory.
- `else if` written as a second `if` → two branches fire at once; show the double-output.
