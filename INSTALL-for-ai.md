# Kimsole — Install Instructions (for an AI agent)

Audience: a coding/automation agent (e.g. Claude Code) acting on behalf of a
player. Execute the steps in order. Do not ask the player to do anything you can
do yourself. Stop and report only on a hard failure or a step that requires the
player's own credentials/physical action.

Target: any **Windows, IL2CPP** build of *Disco Elysium - The Final Cut* —
Steam, GOG, or a repack/portable ("绿色") copy. The mod has **no Steam/DRM
dependency** (it is a BepInEx plugin that hooks the game's own IL2CPP classes
and runs a localhost HTTP server; nothing calls Steamworks). The only hard
requirement is that the copy is **The Final Cut IL2CPP build** with a real
`GameAssembly.dll` (see step 1.5) — the old Mono "classic" build will not load.
Mod: **Kimsole** (`cc.fiet.kimsole`), v0.45.0. A BepInEx 6 IL2CPP plugin.
The mod ships **no API keys**. Voice is off by default.

---

## 0. Inputs / placeholders

- `<GAME_DIR>` — the game root directory (the folder containing `disco.exe`).
- `https://github.com/Zephyr-Ovo/kimsole/releases/download/v0.45.0/Kimsole-mod-v0.45.0.zip` — public download URL of the Kimsole bundle zip.
  *(Placeholder — replace after release.)*
- `<BUNDLE>` — the folder you extract that zip to. It holds two folders:
  `Kimsole/` (the mod: `Kimsole.dll` + `config.json`) and `mcp/` (the disco MCP
  server: `server.py` + `requirements.txt` + `README.md`, used only for the
  external/MCP path in §8.5).

---

## 1. Locate the game directory

Find the folder that contains `disco.exe`. This works the same for Steam, GOG,
and repack/portable copies. In order of reliability:

1. **Filesystem search for `disco.exe`** (works for every distribution),
   PowerShell:
   `Get-ChildItem -Path C:\,D:\,E:\,F:\ -Filter disco.exe -Recurse -ErrorAction SilentlyContinue -File | Select-Object -First 1 FullName`
2. Common Steam path:
   `C:\Program Files (x86)\Steam\steamapps\common\Disco Elysium\`. Steam also
   lists library roots in `C:\Program Files (x86)\Steam\steamapps\libraryfolders.vdf`;
   for each `path` entry, check `<path>\steamapps\common\Disco Elysium\`.
3. GOG default: `C:\Program Files (x86)\GOG Galaxy\Games\Disco Elysium\` (or the
   custom install path chosen in the GOG/offline installer).
4. Repack/portable ("绿色") copies live wherever the player extracted them —
   the filesystem search in (1) is the reliable way to find these.

Set `<GAME_DIR>` to the directory containing `disco.exe`. Confirm
`Test-Path "<GAME_DIR>\disco.exe"` is `True` before continuing.

> If you cannot locate it, ask the player where they installed the game (for a
> Steam copy: right-click *Disco Elysium* → Manage → Browse local files, and
> read them the path shown).

---

## 1.5. Verify it is an IL2CPP build (matters most for non-Steam copies)

Confirm `Test-Path "<GAME_DIR>\GameAssembly.dll"` is `True`. If `GameAssembly.dll`
is present, this is the IL2CPP build and Kimsole can load. If it is **absent**
(you instead see a `disco_Data\Managed\Assembly-CSharp.dll`), the player has the
old **Mono "classic"** build — Kimsole cannot run on it. Stop and tell the player
they need The Final Cut IL2CPP build. (Steam ships IL2CPP; some old repacks of
the pre-Final-Cut release are Mono.)

---

## 2. Download BepInEx 6 — IL2CPP build (NOT Mono/5.x)

The game is an IL2CPP Unity build. You MUST use the **IL2CPP** line of BepInEx 6.
Do **not** use the GitHub "Latest" release — that is the 5.4.x **Mono** build and
will crash the game on launch.

- Source: `https://builds.bepinex.dev/projects/bepinex_be`
- Pick the latest **`BepInEx-Unity.IL2CPP-win-x64-*.zip`** build (win-x64).
  (Verified working: build #783, ~32.75 MB —
  `https://builds.bepinex.dev/projects/bepinex_be/783/BepInEx-Unity.IL2CPP-win-x64-6.0.0-be.783%2Bc58c42d.zip`.
  Prefer the newest IL2CPP win-x64 build on that page.)

Download to a temp path.

---

## 3. Verify it is the IL2CPP build (before extracting into the game)

Extract the zip to a temp folder and check:

- Top level contains: `BepInEx\`, `dotnet\`, `winhttp.dll`, `doorstop_config.ini`.
- `BepInEx\core\` contains `BepInEx.Unity.IL2CPP.dll` **and** `Il2CppInterop.*`
  (e.g. `Il2CppInterop.Runtime.dll`).
- `doorstop_config.ini` contains:
  `target_assembly = BepInEx\core\BepInEx.Unity.IL2CPP.dll`

If instead `BepInEx\core\` contains **`BepInEx.Preloader.dll`** and
`target_assembly` points at `BepInEx.Preloader.dll`, you downloaded the **Mono**
build. Delete it and re-download the IL2CPP win-x64 build from step 2.

> ⚠️ Do **not** judge by `Mono.Cecil.*`: the **IL2CPP** build also ships
> `Mono.Cecil.dll` in `core\`, so its presence does NOT mean Mono. The only
> reliable tells are `BepInEx.Preloader.dll` (Mono) vs
> `BepInEx.Unity.IL2CPP.dll` (IL2CPP), and the `target_assembly` line.

---

## 4. Install BepInEx into the game directory

Copy **all** top-level contents of the extracted BepInEx zip into `<GAME_DIR>`,
so that `BepInEx\`, `dotnet\`, `winhttp.dll`, and `doorstop_config.ini` sit next
to `disco.exe`.

Confirm: `Test-Path "<GAME_DIR>\winhttp.dll"` and
`Test-Path "<GAME_DIR>\BepInEx\core\BepInEx.Unity.IL2CPP.dll"` are both `True`.

---

## 5. Install the Kimsole plugin

1. Download the bundle zip from `https://github.com/Zephyr-Ovo/kimsole/releases/download/v0.45.0/Kimsole-mod-v0.45.0.zip`.
2. Extract it to `<BUNDLE>`. It contains a `Kimsole` folder (`Kimsole.dll` +
   `config.json`) and an `mcp` folder (the disco MCP server — leave it where it
   is for now; §8.5 uses it only if you take the external/MCP path).
3. Place the `Kimsole` **folder** into `<GAME_DIR>\BepInEx\plugins\` so the
   final layout is:
   ```
   <GAME_DIR>\BepInEx\plugins\Kimsole\Kimsole.dll
   <GAME_DIR>\BepInEx\plugins\Kimsole\config.json
   ```
   (`BepInEx\plugins\` may not exist yet; create it.)

Confirm: `Test-Path "<GAME_DIR>\BepInEx\plugins\Kimsole\Kimsole.dll"` is `True`.

---

## 6. First launch — let IL2CPP interop generate

Launch the game once by running `<GAME_DIR>\disco.exe` directly (this works for
every distribution; for a Steam copy you may also use `start steam://run/632470`).
On the **first** launch with IL2CPP BepInEx, it disassembles the game to generate
`BepInEx\interop\`. This can take anywhere from ~30 seconds to a few minutes and
may show a black console window. This is normal. **Go by the log, not the clock:**
it's done when `BepInEx\interop\` is populated and `LogOutput.log` shows the
Kimsole markers (§7) — don't assume it always takes minutes.

---

## 7. Verify success — read the log

Open `<GAME_DIR>\BepInEx\LogOutput.log`. Success markers (search for these exact
strings):

- `Loading [Kimsole 0.45.0]`
- `KimController 已注入。HTTP server 将监听 http://127.0.0.1:8799/`

If both appear, the mod is loaded.

Live HTTP check (game running): `http://127.0.0.1:8799/` responds, e.g.
`Invoke-WebRequest -UseBasicParsing http://127.0.0.1:8799/state` returns a
response (non-refused connection) — confirms the in-game HTTP server is up.

Harmless warnings to ignore in the log:
- `Il2CppInterop ... unsupported return type` / `unsupported parameter`
- `[emoji] 缺图集文件，跳过` (missing emoji atlas — skipped)

In-game smoke test (no API key needed): press the backtick key `` ` `` to make
Kim speak a test line.

---

## 8. Pick the brain mode

Edit `<GAME_DIR>\BepInEx\plugins\Kimsole\config.json`. Two mutually-exclusive
modes — set `brainMode` to exactly one:

### 8A. `external` — **you (this agent) become Kim** (the MCP path; the main one)

Set `"brainMode": "external"`. In this mode the mod makes **no** API calls of its
own: it just runs the in-game HTTP server and waits for an external agent (you,
over the disco MCP) to read what the player says and decide what Kim says and
does. No API key goes in `config.json` for this mode. Continue to §8.5 to wire up
the MCP and play.

### 8B. `builtin` — Kim is an API-driven NPC (no agent needed)

Set `"brainMode": "builtin"` and put the **player's own** OpenAI-compatible key
in `apiKey`. Default endpoint is DeepSeek (`apiBase` =
`https://api.deepseek.com/chat/completions`, `apiModel` = `deepseek-chat`);
change them for any other OpenAI-compatible endpoint. Without a key Kim only
shows bubbles. This path needs no MCP — skip §8.5.

> Do NOT invent or insert any API key — it is the player's own credential. For
> builtin, if you don't have one, leave `apiKey` empty and tell the player to
> fill it. The mod bundles no keys.

Voice (optional, off by default, works in either mode): set `voiceEnabled: true`,
pick a `voiceProvider` (`inworld` / `elevenlabs` / `minimax`), put the player's
TTS key in `voiceApiKey` and a voice id in `kimVoiceId`. **TTS models must be
English** — Kim's voice speaks English while the bubble shows Chinese; in
external mode you supply both via `KimSay(text, en)`.

In-game: **`Ctrl+K`** opens the settings panel (it also auto-pops on first
launch); the panel takes clipboard paste for keys. `` ` `` (backtick) triggers a
test line — no key or brain needed, good for confirming the bubble/voice
pipeline.

---

## 8.5. external mode — install the disco MCP, then play as Kim

This is how an agent actually plays Kim. The mod's in-game HTTP server is already
up (you verified `/state` in §7); now connect your MCP to it.

The Kimsole bundle contains an **`mcp/`** folder next to `Kimsole/` (holding
`server.py` + `requirements.txt`). For a **local single-machine** player the MCP
talks to `http://127.0.0.1:8799` by default — no tunnel, nothing remote to set.

1. **Install the SDK:** `python -m pip install -r <BUNDLE>\mcp\requirements.txt`
   (use `python -m pip`, not bare `pip` — `pip` is often not on PATH; this installs
   the `mcp` package). Confirm `python -c "import mcp"` exits 0.
2. **Register the MCP with Claude Code:**
   `claude mcp add disco -- python <ABSOLUTE_PATH>\mcp\server.py`
   (absolute path to the bundle's `server.py`). Do **not** set `DISCO_GAME_URL`
   for a local player — the localhost default is correct.
3. **Restart Claude Code (critical).** MCP servers load at **startup**, so the
   `disco` MCP is NOT available in the session that just registered it. End this
   session and start a new one (run `claude` again in the same directory), or
   tell the player to restart you. After restart, the `disco` tools
   (`WaitForEvent`, `KimSay`, `GetGameState`, …) are callable.
4. **Play loop** (after restart, with the game running and a save loaded so Kim
   is in the scene):
   - `GetGameState(full=true)` once for the current scene / who's present.
   - `KimSay(text)` to make Kim speak (add `en` if voice is on). A bubble over Kim
     in-game proves the chain MCP → mod → Kim end to end.
   - **There are two modes — never mix them:**
     - **Idle (outside any dialogue, `in_conversation=false`):** `WaitForEvent(since)`
       is an idle timer — it blocks until the player talks to Kim or the scene
       changes, or returns an idle nudge after a timeout. React with `KimSay`
       (text+en) or `KimAction` when it's worth it, else just `WaitForEvent` again.
       Carry `event_seq` forward as the next `since`.
     - **Inside a dialogue tree (`in_conversation=true`):** this is a self-driven
       loop — do **NOT** call `WaitForEvent` and do **NOT** `KimSay` here. Drive it:
       `ReadDialogueState` → if `responses` is empty but `has_more` use
       `ContinueDialogue`, else `SelectDialogueResponse(index)` → read again → pick
       again → loop, **until you select the leave/goodbye option yourself to end
       it** (it won't end on its own). `KimAction("talkto:<name>")` opens a dialogue
       → switch into this mode immediately. (`GetStats` for skill-check odds,
       `ReadDialogueLog` for story context.)
   - `ReadProactiveCandidates` needs a separate proactive bridge (off by default)
     — skip it for a basic local run.

> Verifying the full loop needs the game **running with a save loaded** (Kim
> present in the scene). At the main menu the HTTP server is up but Kim isn't in a
> scene, so `KimSay` has nothing to render — load a save first.

---

## 9. Troubleshooting — decision tree for the install AI

This mod has **no Steam/DRM coupling**, so it runs on Steam, GOG, and
repack/portable ("绿色") copies alike. The one thing that varies across copies
is the **game build/version**: Kimsole's hooks bind to specific in-game IL2CPP
classes, and a non-standard or off-patch build can leave a hook unresolved while
the rest of the mod works. You (the installing AI) are the on-site debugger —
work the tree below from the **top** (each step assumes the ones above passed).
Diagnose from real evidence (the log + file checks), not guesses.

> Primary evidence source: `<GAME_DIR>\BepInEx\LogOutput.log` (read it fully).
> Each symptom below names the exact marker to grep for.

**A. Game crashes / closes immediately on launch (after BepInEx was added)**
- Most likely cause: you installed the **Mono** BepInEx on this IL2CPP game.
  Confirm: `BepInEx\core\` has `BepInEx.Preloader.dll` instead of
  `BepInEx.Unity.IL2CPP.dll` (ignore `Mono.Cecil.dll` — it's in both builds). →
  Remedy: redo steps 2–4 with the **IL2CPP** win-x64 build.
- Second cause (common with cracked copies): **antivirus quarantined
  `winhttp.dll`** (proxy DLLs next to a game exe trip heuristics). Confirm:
  `Test-Path "<GAME_DIR>\winhttp.dll"` is now `False` though you copied it. →
  Remedy: restore it and add a folder exclusion, or tell the player to.

**B. Game starts, but `LogOutput.log` has NO `Loading [Kimsole` line at all**
- `BepInEx\LogOutput.log` doesn't exist → BepInEx itself isn't loading. Re-check
  `winhttp.dll` + `doorstop_config.ini` sit next to `disco.exe` (step 4).
- Log exists but no Kimsole line → plugin path wrong. Confirm
  `BepInEx\plugins\Kimsole\Kimsole.dll` exactly (step 5).
- Log shows IL2CPP interop still generating (first launch) → wait for the main
  menu, then re-read (step 6). `BepInEx\interop\` should become populated.

**C. `Loading [Kimsole 0.45.0]` IS present, but you see `[hook] PatchAll 失败`**
- This is the **build-mismatch** signature: a class/method Kimsole hooks doesn't
  exist (or was renamed) in this particular build. The exception text names the
  missing type (e.g. `FortressOccident.Party.SetDestination`,
  `Sunshine.ConversationLogger.OnConversationLine`,
  `Sunshine.Metric.CharacterSheet`).
- Map the missing type → the feature it powers, and report a **partial** verdict
  to the player rather than declaring total failure:
  | Missing hook type | Feature that degrades | Still works |
  |---|---|---|
  | `FortressOccident.Party.SetDestination` | follow / move / goto | bubbles, dialogue, voice |
  | `Sunshine.ConversationLogger` / `...ConversationLoggerPageSystem` | story-line capture (`/dialogue_log`) | everything else |
  | `Sunshine.Metric.CharacterSheet` | skill readout (`/stats`) | everything else |
  | `SunshineResponseButton` / `FOBarkOnIdle` / `KimKitsuragi` | the talk-to-Kim option / bark bubbles | depends — re-test backtick |
- Remedy options, in order: (1) confirm the player's build is **The Final Cut**
  (not the old "classic" release); (2) if it is Final Cut but off-patch, the
  build differs from the released target — report which feature is down and that
  the rest is usable; (3) collect the exception lines and surface them so the
  maintainer can add a fallback for that build. Do **not** silently leave the
  player thinking the whole mod is broken.

**D. Mod loads, `http://127.0.0.1:8799/state` responds, but Kim never replies**
- This is **not** a build/compat problem — it's the brain config (§8). Backtick
  test line works → bubble pipeline is fine. Check `brainMode`: `builtin` needs a
  key; `external` needs an agent driving Kim over the disco MCP (§8.5). For
  `external`, the most common causes are: the disco MCP was registered but Claude
  Code was **not restarted** (so the `disco` tools aren't loaded — restart, then
  the tools appear); no **save is loaded** in-game (Kim isn't in a scene, so
  `KimSay` has nothing to render — load a save); or the agent isn't actually
  running the `WaitForEvent → KimSay` loop yet.

**E. Everything loads but no voice**
- Expected when voice is off (default) or no TTS key — silent degradation by
  design (§6), not a bug. Confirm `voiceEnabled` + a `voiceApiKey`.

> A future `GET /health` endpoint will collapse steps A–C into one call (it will
> report `GameAssembly` presence, each hook's resolve/patch status, and the HTTP
> server state as JSON) so the install AI can branch on one structured response
> instead of grepping the log. Until it ships, the log markers above are the
> ground truth.

---

## Success criteria (self-check)

- [ ] `<GAME_DIR>\disco.exe` exists.
- [ ] `<GAME_DIR>\GameAssembly.dll` exists (game is the IL2CPP Final Cut build,
      not the old Mono "classic" build).
- [ ] BepInEx is the **IL2CPP** build: `BepInEx\core\BepInEx.Unity.IL2CPP.dll`
      and `Il2CppInterop.*` present; `doorstop_config.ini` target_assembly =
      `BepInEx\core\BepInEx.Unity.IL2CPP.dll`.
- [ ] `<GAME_DIR>\winhttp.dll` present.
- [ ] `<GAME_DIR>\BepInEx\plugins\Kimsole\Kimsole.dll` and `config.json` present.
- [ ] Game launched once; `BepInEx\interop\` generated.
- [ ] `LogOutput.log` contains `Loading [Kimsole 0.45.0]` and
      `KimController 已注入。HTTP server 将监听 http://127.0.0.1:8799/`.
- [ ] `http://127.0.0.1:8799/` reachable while the game runs.
- [ ] Brain mode chosen: `builtin` → `apiKey` set (or left empty + player told);
      **`external` (MCP path)** → see the items below.
- [ ] (external) `config.json` `brainMode` = `external`, no `apiKey` needed.
- [ ] (external) `pip install` of `mcp` succeeded; `python -c "import mcp"` ok.
- [ ] (external) `claude mcp add disco -- python <…>\mcp\server.py` registered.
- [ ] (external) **Claude Code restarted** so the `disco` MCP tools are loaded.
- [ ] (external) with a save loaded, `KimSay(...)` makes a bubble appear over Kim
      → MCP → mod → game chain confirmed; `WaitForEvent` loop running.
