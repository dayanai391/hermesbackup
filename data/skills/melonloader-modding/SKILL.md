---
name: melonloader-modding
description: Build MelonLoader net6 mods without game assemblies.
version: 0.1.0
author: Dayan, Hermes Agent
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [modding, unity, il2cpp, melonloader, harmony, reverse-engineering]
    related_skills: [github]
---

# MelonLoader Modding Skill

Research and ship compiling MelonLoader (net6) mods for Unity IL2CPP games from
this Linux host — without the game's own DLLs on disk. Deliverable is always a
zip: built DLL + source + csproj + README, explained in Persian.

## When to Use
- User wants a mod/cheat for a Unity (IL2CPP) game with MelonLoader installed.
- Don't use for: Mono games (BepInEx instead), engine-agnostic trainers, or
  accounts/evasion work tied to game platforms.

## Procedure
1. **Research before writing a line** — `web_search` for an existing newer mod
   version AND for built-in cheat codes (community code lists live on F95Zone
   thread page-1 spoilers, platinmods how-tos, itch.io devlogs, APK mirror sites).
   A working code is the easiest fix — deliver it as option (a) first.
2. **Build env (once per host)**:
   - `terminal`: install SDK to `/opt/data/.dotnet` via `dot.net/v1/dotnet-install.sh --channel 8.0`,
     always run with `TMPDIR=/opt/data/tmp NUGET_PACKAGES=/opt/data/nuget DOTNET_SYSTEM_GLOBALIZATION_INVARIANT=1`
     (ICU is absent → dotnet FailFasts otherwise; `/data` is tiny, keep SDK/cache off it).
   - Fetch the matching `MelonLoader.x64.zip` from GitHub releases; extract with
     **python zipfile** (`unzip` is not installed).
3. **Design reflection-first**: never reference `Assembly-CSharp.dll` (absent here,
   and it pins the mod to one game build). Find types at runtime:
   `AppDomain.CurrentDomain.GetAssemblies()` → assembly named `Assembly-CSharp`,
   then scan methods by name/signature and `harmony.Patch(...)` manually.
4. **Patch rules**: only `__result` / `__instance` / `object[] __args` hooks —
   never declare a parameter named like an original game parameter (see Pitfalls).
   Transform save-data objects by walking fields+properties with reflection.
5. **Build**: `dotnet build -c Release -p:MLDir=<path-to-MelonLoader/net6>`; csproj
   targets `net6.0`, HintPath under an `MLDir` property, `<Private>false</Private>`,
   zero NuGet packages.
6. **Ship with a feedback loop**: mod writes a first-run dump file (member list of
   the key game types + list of applied patches) into the Mods folder, so a v2 can
   be pinned exactly if some check still blocks. Include an auto-backup of the
   user's save folder before touching it.
7. **Deliver** as one zip (DLL + .cs + .csproj + README + cfg) via a `MEDIA:` line;
   README in Persian with install path, build command, and config keys.

## Pitfalls
- Harmony binds prefix/postfix parameters to the original method **by name**; a
  rename upstream silently breaks the patch (this is exactly how Avva v6 failed).
  Positional/special hooks (`__0` is NOT safe across versions either) — stick to
  `__result`/`__instance`/`object[] __args`.
- Il2Cpp collection wrappers do not implement CLR `IEnumerable` — enumerate via
  reflection: `Count` property + `Item` indexer; for dictionaries use
  `Keys` + `get_Item`/`set_Item`.
- If the delivery involves scripted git pushes, load the `github` skill and
  follow its automated-backups reference (git quirks and token handling live there).

## Verification
- Build exits 0 with 0 warnings/errors before claiming done.
- Zip contains the DLL plus full source; `MEDIA:` line included in the reply.
- First real run: user confirms the mod log line (`patched N methods`) and checks
  the dump file exists for any follow-up pinning.
