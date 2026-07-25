# imagegen-0-143-0

<p align="center">
  <strong>Stable Codex imagegen, delegated from your agent.</strong>
</p>

<p align="center">
  <a href="README.md">English</a> ·
  <a href="skills/imagegen-0-143-0">Browse skill</a>
</p>

![Stable Codex imagegen, delegated from your agent](assets/hero.jpg)

這個 skill 把圖片生成與編輯固定在 `@openai/codex@0.143.0`。你的 coding agent 載入 `$imagegen-0-143-0` 後，會把實際產圖工作委派給該固定 Codex 路徑，讓不同 session 的行為可重複。

適用 **Codex**、**Claude Code**、**Grok**。

## 安裝

把 `skills/imagegen-0-143-0` 複製到各 agent 會載入的 skills 目錄：

| Agent | 個人（全專案） | 僅此專案 |
| --- | --- | --- |
| Codex | `~/.codex/skills/` | 你的 Codex loader 使用的 project skills 路徑 |
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| Grok | `~/.grok/skills/` 或 `~/.agents/skills/` | `.grok/skills/` |

```bash
# 在此 repo 根目錄執行
cp -R skills/imagegen-0-143-0 ~/.claude/skills/imagegen-0-143-0   # Claude Code
cp -R skills/imagegen-0-143-0 ~/.grok/skills/imagegen-0-143-0     # Grok
cp -R skills/imagegen-0-143-0 ~/.agents/skills/imagegen-0-143-0   # 共用 / 多 agent
cp -R skills/imagegen-0-143-0 ~/.codex/skills/imagegen-0-143-0    # Codex
```

**前置條件：** 已登入的 Codex / ChatGPT session，讓固定版 CLI 能執行 imagegen。

## 在 coding agent 裡使用

用一般對話下指令，並點名 skill。Agent 會載入 `$imagegen-0-143-0` 並執行固定版本流程。

### Codex

```text
Use $imagegen-0-143-0 to generate a polished marketing image of Hong Kong
Victoria Harbour at golden hour. Save it to ./generated.
```

```text
Use $imagegen-0-143-0 to edit /absolute/path/to/product.png: replace the
background with a clean studio setting. Keep product shape and camera angle.
Save to ./generated.
```

### Claude Code

安裝到 `~/.claude/skills/` 或 `.claude/skills/` 後：

```text
Use the imagegen-0-143-0 skill to generate a premium product launch hero image
for a modern AI automation platform. Clean professional workspace, subtle screen
glow, room for a website headline. Save to ./generated.
```

```text
$imagegen-0-143-0 — edit ./assets/product.png, studio background, keep the
product unchanged. Save to ./generated.
```

### Grok

安裝到 `~/.grok/skills/`、`~/.agents/skills/`，或專案 `.grok/skills/`。用 `$imagegen-0-143-0`，或從 `/skills` 選取：

```text
Use $imagegen-0-143-0 to generate a website hero. Style reference:
./refs/style.jpg. Layout reference: ./refs/layout.png. Same mood and composition,
do not copy the references. Save to ./generated.
```

```text
$imagegen-0-143-0 generate a photorealistic desk setup for a SaaS landing page,
soft morning light. Save absolute path when done.
```

## Prompt 模式

三個 agent 可用相同寫法。本機圖片建議用絕對路徑。

**生成**
```text
Use $imagegen-0-143-0 to generate <你的圖片 prompt>。Save it to ./generated.
```

**編輯**
```text
Use $imagegen-0-143-0 to edit /absolute/path/to/source.png: <編輯說明>。
Save to ./generated.
```

**參考圖導向**
```text
Use $imagegen-0-143-0 with /absolute/path/to/style.jpg and
/absolute/path/to/layout.png as references. Generate <你的圖片 prompt>。
Do not copy the references. Save to ./generated.
```

## 規則

- 除非使用者要求，不要重寫、翻譯或「優化」圖片 prompt
- 檔案對應與存檔路徑放在執行指令，不要寫進圖片 prompt
- 未指定時預設存到 `./generated`
- 回報最終絕對輸出路徑

## 為什麼固定 0.143.0

最新版 Codex 圖片路徑可能出現：

```text
image generation failed: network error: error sending request for url
(https://chatgpt.com/backend-api/codex/images/generations)
```

此 skill 會把圖片工作委派給固定 `@openai/codex@0.143.0` 的子行程：prompt 原封不動、輸入用絕對路徑 `-i`、輸出路徑明確。

若仍出現該網路錯誤，請檢查連線、VPN／proxy、防火牆，以及已登入的 Codex session 是否能存取 `chatgpt.com`。

Skill 作者用的內部 CLI 細節：[skills/imagegen-0-143-0/references/usage.md](skills/imagegen-0-143-0/references/usage.md)
