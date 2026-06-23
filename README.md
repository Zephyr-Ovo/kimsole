# Kimsole

**Kim Kitsuragi as an AI partner you can actually talk to — live, in your own
playthrough of *Disco Elysium - The Final Cut*.**

Press a key, say something to Kim, and he answers in his own register: measured,
dry, quietly warm. Plug in your own AI and Kim becomes a companion who reacts to
what you're doing — he can speak out loud (your own TTS, subtitle in your
language), and in **agent mode** even walk over to people, start and carry
conversations, follow you, and ride along as you work Martinaise.

You bring your own AI. **Kimsole ships no API keys and bundles no AI or voice
service** — whatever you type, plus a little game context, goes only to the
service *you* configure. The author receives nothing; no telemetry, no accounts,
it runs on your machine.

Kimsole is a **BepInEx 6 (IL2CPP)** plugin. *Disco Elysium has no Steam Workshop —
every mod is a manual file copy.*

## Two ways to play

- **Simple (builtin):** drop in any OpenAI-compatible API key (e.g. DeepSeek) and
  Kim responds to you on his own.
- **Agent (MCP):** point your own coding agent (e.g. Claude Code) at the included
  MCP server and *your* AI plays Kim for real — reading the scene, choosing his
  lines, driving dialogues, acting in the world.

## Install

1. Install **BepInEx 6 — the IL2CPP build** (NOT Mono / 5.x) from
   <https://builds.bepinex.dev/projects/bepinex_be> into your game folder (next to
   `disco.exe`).
2. Launch the game once, reach the main menu, then quit (it generates
   `BepInEx\interop\`; a black console window is normal).
3. Download the latest **Kimsole** release and put the `Kimsole` folder into
   `<game>\BepInEx\plugins\`.
4. Launch, load a save, press **`Ctrl+K`**, choose **builtin**, paste your API
   key, Save.
5. Press the backtick key `` ` `` — Kim should pop a test bubble. In any dialogue,
   a `[talk to Kim]` option appears at the bottom of your responses.

- **Full human walk-through (中文):** [TUTORIAL.zh-CN.md](./TUTORIAL.zh-CN.md)
- **For Claude Code / agent users (auto-install + MCP play):**
  [INSTALL-for-ai.md](./INSTALL-for-ai.md) — hand this file to your AI and it
  installs and wires everything itself.

## Requirements

- *Disco Elysium - The Final Cut*, the **IL2CPP** build (Steam ships it; it has a
  `GameAssembly.dll`). The old Mono "classic" build is not supported.
- **BepInEx 6 — IL2CPP (win-x64)**.
- *builtin mode:* an OpenAI-compatible API key.
- *agent mode:* your own coding agent + the included disco MCP (Python,
  `pip install mcp`).
- *voice (optional):* an ElevenLabs / Inworld / MiniMax key + a voice id (English
  TTS models — Kim speaks English, subtitle shows your language).

## Privacy

This mod sends your messages and limited game state to the external AI / TTS
service **you** configure, and nowhere else. No keys are bundled; nothing is sent
to the mod author. Only enable what you're comfortable sharing.

## Credits

ZA/UM for *Disco Elysium* and Kim Kitsuragi · BepInEx / HarmonyX / Il2CppInterop ·
the player-AI / Claude Code community.
