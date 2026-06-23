# Kimsole 玩家上手教程（从打开 Steam 开始）

> 让金·曷城成为《极乐迪斯科》里能和你对话的 AI 搭档。本教程面向第一次装 mod 的玩家，从打开 Steam 一步步走到「在游戏里和金说上话」。全程约 15 分钟。

本 mod 名为 **Kimsole**(`cc.fiet.kimsole`)，当前版本 **v0.45.0**。它**不附带任何 AI / 语音服务**——你用自己的 API key，数据发往你自己配置的服务，作者收不到任何东西。

---

## 你需要准备什么

- 一份 **Steam 正版《极乐迪斯科》(Disco Elysium - The Final Cut)**。
- 一个 **AI 模型的 API key**（任意 OpenAI 兼容端点都行；默认配置指向 DeepSeek，便宜好上手）。没有 key 也能装，只是金不会主动回应，只显示气泡。
- （可选）一个 **ElevenLabs** 账号的 key，如果你想让金有真人语音。
- 大约 15 分钟。

> 本 mod 是 BepInEx 插件，不是创意工坊物品。**《极乐迪斯科》没有 Steam 创意工坊**，所有 mod 都靠手动放文件安装——别去工坊找，找不到。

---

## 第 1 步：找到游戏的安装文件夹

1. 打开 **Steam**，进入「库」。
2. 在左侧列表里 **右键点《极乐迪斯科》** → **管理 (Manage)** → **浏览本地文件 (Browse local files)**。
3. 弹出的文件夹就是游戏目录。里面应该能看到 `disco.exe`（游戏主程序）。**记住这个文件夹**，后面所有操作都在这里。

---

## 第 2 步：安装 BepInEx 6（IL2CPP 版）

Kimsole 依赖 **BepInEx** 这个 mod 加载框架。《极乐迪斯科》是 IL2CPP 引擎，所以**必须用 IL2CPP 版的 BepInEx 6**（不是 Mono 版，别下错）。

> ⚠️ **千万别去 GitHub releases 页找。** 那个页面置顶的「Latest」是 **5.4.x 的 Mono 版**——下了它游戏会闪退。IL2CPP 版只在下面这个构建站发布。**别下 Mono / 5.x。**

1. 打开 BepInEx 的 IL2CPP 构建站：**https://builds.bepinex.dev/projects/bepinex_be**
2. 在列表里找最新的一条，下载文件名形如 **`BepInEx-Unity.IL2CPP-win-x64-*.zip`** 的那个（取该页**最新**的 IL2CPP win-x64 构建即可，文件约 32 MB）。
   （认准三个关键词：`IL2CPP` + `win-x64` + `6.0.0`。如果文件名里有 `Mono`、或版本是 `5.x`，那是错的，别下。）
3. 把这个 zip **解压，把里面的所有内容直接放进第 1 步的游戏目录**——让 `BepInEx` 文件夹、`dotnet` 文件夹、`winhttp.dll`、`doorstop_config.ini` 等和 `disco.exe` 并排。
4. **下对没？快速自检：** 解压后顶层应有 `BepInEx\`、`dotnet\`、`winhttp.dll`、`doorstop_config.ini`；点进 `BepInEx\core\`，里面应能看到 `BepInEx.Unity.IL2CPP.dll` 和一堆 `Il2CppInterop.*`。如果 `core` 里是 `BepInEx.Preloader.dll` 加一堆 `Mono.Cecil.*`，说明你下成了 Mono 版——删掉，回第 1 步重下。
5. **先启动一次游戏，等进到主菜单，再退出。** IL2CPP 版第一次启动会反汇编游戏、自动生成 `BepInEx\interop\` 等一批文件——**这一步可能要几分钟，还会弹个黑色控制台窗口，都是正常的**，耐心等它进到主菜单。没有这一步 mod 装不上。

> 验证：游戏目录下出现了 `BepInEx\plugins\` 和 `BepInEx\interop\` 文件夹 = BepInEx 装好了。

---

## 第 3 步：安装 Kimsole

1. 解压 **`Kimsole-mod-v0.45.0.zip`**，里面有一个 **`Kimsole` 文件夹**（内含 `Kimsole.dll` 和 `config.json`）。
2. 把整个 **`Kimsole` 文件夹**放进：
   `游戏目录\BepInEx\plugins\`
3. 装好后路径应该是这样：
   ```
   游戏目录\BepInEx\plugins\Kimsole\Kimsole.dll
   游戏目录\BepInEx\plugins\Kimsole\config.json
   ```

> 注意是把 `Kimsole` **文件夹**放进 `plugins`，不是把两个文件散着丢进去。

---

## 第 4 步：启动游戏，打开设置面板（`Ctrl+K`）

1. 启动《极乐迪斯科》。
2. 进入任意存档（新游戏或继续都行）。**第一次安装会自动弹出 Kimsole 设置面板**；之后任意时刻按 **`Ctrl+K`** 开关它。
3. 在面板里：
   - **大脑模式**选 **builtin（内置）** —— 用你自己的 API key 让金自动回应，最简单。
   - 把你的 **API key 粘贴**进去（面板支持从剪贴板粘贴，免得在游戏里手打一长串）。
   - 默认端点是 DeepSeek；想用别的 OpenAI 兼容服务，改 `apiBase` / `apiModel` 即可（也可以稍后直接编辑 `config.json`）。
   - **语音**先留着关（下面有可选章节再开）。
4. 点 **保存**。

### 从哪弄 API key?

builtin 模式需要一个 **OpenAI 兼容**的 key。最省事的是 DeepSeek（默认端点就是它）：去 `platform.deepseek.com` 注册、充一点点钱、生成一个 API key，形如 `sk-...`。任何兼容 OpenAI `/chat/completions` 协议的服务（OpenAI、各类中转、本地 Ollama 等）都能用，改 `apiBase`/`apiModel` 指过去就行。

---

## 第 5 步：冒烟测试——按反引号键 `` ` ``

在游戏里（不需要进对话）**按键盘左上角的反引号键 `` ` ``**（数字 1 左边、Esc 下面那个键）。

- ✅ 金头顶冒出一句 **「【测试】哈里，听得到吗？」** 的气泡 → **mod 加载成功**。
- ✅ 如果你开了语音，还应该**同时听到金的声音**。
- ❌ 什么都没有 → 跳到本页底部「排错」。

> 这一步只验证 mod 本身和语音通路，不需要 API key——所以先过这关，再去配大脑。

---

## 第 6 步：真正和金对话——「对金说」

1. 在游戏里 **进入任意一段对话**（找个 NPC 说话，或点开一个能触发对话的物件）。
2. 在你的回复选项列表里，**最底部会多出一条 `[对金说]` 选项**（这是 mod 注入的）。
3. **点它** → 弹出一个输入框 → **打字**输入你想对金说的话 → 按 **回车** 发送。
4. 稍等一两秒，金会用他的口吻回应，气泡显示在他头顶（开了语音就同时发声）。

就这样，你已经能在查案途中随时拉金聊两句了。

---

## （可选）开启语音

想让金有真人嗓音，需要你自己的 **TTS key**:

1. 按 `Ctrl+K` 打开面板，勾选 **「开启金的语音」**。
2. **选 TTS 厂商**（面板上的「厂商」一行，三选一）：**Inworld / ElevenLabs / MiniMax**。
3. **TTS Key** 填你这家厂商的 key；**声线 ID** 填你要用的声音 id：
   - **ElevenLabs**：在声音库挑一个，或用你克隆的声音，复制它的 Voice ID。
   - **MiniMax**：选 MiniMax 时面板会多出一个 **Group ID** 框，一并填上。
4. 保存，回第 5 步按 `` ` `` 测一句听听。

> ⚠️ **只支持英文 TTS 模型**：金的语音念的是**英文**。设计如此——气泡显**中文字幕**、配音念**英文**：开了语音后，驱动金的 AI（你 key 那个内置大脑，或外接的 agent）会自动同时给出中英两版，mod 把中文显在气泡、把英文交给 TTS 念。所以**声线选英文声音**。
>
> 没填 TTS key（或没勾语音）→ 金只显文字气泡、不发声（纯展示也能跑）。

---

## 全流程自测清单

照这个清单走一遍，每条打勾就说明通了：

- [ ] Steam 右键 → 浏览本地文件，找到了含 `disco.exe` 的游戏目录
- [ ] 下对了 **IL2CPP 版** BepInEx 6，解压进游戏目录
- [ ] 启动游戏一次再退出，目录里出现了 `BepInEx\plugins\`
- [ ] `Kimsole` 文件夹放进了 `BepInEx\plugins\`，路径对
- [ ] 第一次进存档自动弹出设置面板（之后 `Ctrl+K` 开关）
- [ ] 选了 builtin、粘贴了 API key、保存
- [ ] 按 `` ` `` 金冒出测试气泡（mod 加载 OK）
- [ ] 进对话能看到 `[对金说]`，点它能打字，金有回应
- [ ] （可选）开语音后能听到金的声音

全部打勾 = 一个新玩家可以从零跑通。

---

## 排错

**按 `` ` `` 没反应 / 金不出现**
- 多半是 BepInEx 没装好或装成了 Mono 版。确认游戏目录有 `winhttp.dll`、`BepInEx\` 文件夹，且 `BepInEx\plugins\Kimsole\Kimsole.dll` 在位。
- 看 BepInEx 日志：`BepInEx\LogOutput.log`，搜 `Kimsole` / `KimController 已注入`。没有这行 = 插件没被加载。
- BepInEx 6 第一次启动会预处理，可能较慢；给它点时间，或多启动一次。

**金只显气泡、不主动回应**
- builtin 模式但没填 / 填错 `apiKey`；或 `apiBase`/`apiModel` 指向的端点不对。
- 看日志里 `[brain]` 开头的警告。

**看得到 `[对金说]` 但点不动**
- 那是对话 UI 的渲染层问题；换一段对话再试。若稳定复现，记下当时场景反馈给作者。

**语音没声**
- `voiceEnabled` 没设 true，或 `voiceApiKey` / `kimVoiceId` 没填 / 填错。
- 系统音量 / 游戏语音音量是否为 0（金的音量跟随游戏语音音量）。

**游戏闪退**
- 先只装 BepInEx、不装 Kimsole，确认游戏能开 = 区分是 BepInEx 还是 mod 的问题。
- 确认 BepInEx 版本是给《极乐迪斯科》(Unity 2020 / IL2CPP)的那条线。

---

## 发布在哪（给想分享的人）

- **Nexus Mods**（nexusmods.com 的 Disco Elysium 专区）= DE mod 的主要平台，玩家最常在这找。
- **GitHub Releases** = 给会装 BepInEx 的人直接下载，配合 Nexus。
- 不要发到 Steam 创意工坊——这游戏没开工坊。
