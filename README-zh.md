# Codex Imagegen 0.143.0 Skill

這是一個精簡的 Codex skill，用固定版本 `@openai/codex@0.143.0` 來產生或編輯點陣圖片。

這個 repo 的範圍刻意保持很小：當工作流程需要使用 Codex `0.143.0` 內的舊版 `imagegen` 路徑，而不是目前 agent session 內建的圖片工具時，就使用這個 skill。

English: [README.md](README.md)

## 這個 Skill 做什麼

- 透過 `@openai/codex@0.143.0` 執行圖片生成。
- 支援全新圖片生成、圖片編輯、參考圖導向生成。
- 完整保留使用者的圖片 prompt。
- 本機輸入圖片一律用絕對路徑傳入 `-i`。
- 將存檔位置、輸入圖片對應等執行指令，和真正的圖片 prompt 分開。

## 為什麼要做這個 Skill

Codex 的圖片生成能力可能會因版本與執行介面不同而改變。對需要穩定輸出處理、prompt 保真、或相容既有圖片生成流程的團隊來說，直接依賴目前 session 的預設圖片工具不夠明確。

這個 skill 的做法是：把圖片工作委派給一個子 Codex process，並固定使用 `@openai/codex@0.143.0`。

主要目標：

1. 使用者的 prompt 要原封不動傳入。
2. 圖片輸入必須透過絕對路徑明確指定。
3. 生成結果必須回傳清楚的輸出檔案路徑。
4. 工作流程要能在使用 Codex skills 的 repo 之間移植。

## Repo 結構

```text
.
├── AGENTS.md
├── README.md
├── README-zh.md
└── skills/
    └── imagegen-0-143-0/
        ├── SKILL.md
        ├── agents/
        │   └── openai.yaml
        └── references/
            └── usage.md
```

## 快速指南

1. 將這個 repo 放在 Codex skill loader 可讀取的位置。
2. 需要產圖或改圖時，請 Codex 使用 `imagegen-0-143-0`。
3. 提供清楚的圖片 prompt。
4. 如果要編輯圖片或使用參考圖，提供本機圖片路徑。
5. 檢查子 Codex process 回傳的最終絕對輸出路徑。

使用者請求範例：

```text
Use $imagegen-0-143-0 to generate a polished marketing image of Hong Kong Victoria Harbour at golden hour. Save it to ./generated.
```

## 手動執行指令

這個 skill 內部使用的基本模式如下：

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  '$imagegen <verbatim user prompt>

Execution instruction: Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

## 範例：生成新圖片

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  '$imagegen Generate a premium product launch hero image for a modern AI automation platform. Use a clean, professional composition with a real workspace, subtle screen glow, and clear room for website headline text.

Execution instruction: Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

## 範例：編輯既有圖片

每張輸入圖片使用一個 `-i` 參數。請一律使用絕對路徑。

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  -i /absolute/path/to/source.png \
  '$imagegen Replace the background with a clean studio setting. Keep the product shape, label, proportions, and camera angle unchanged.

Execution instruction: Image 1 is /absolute/path/to/source.png. Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

## 範例：使用參考圖生成

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  -i /absolute/path/to/style-reference.jpg \
  -i /absolute/path/to/layout-reference.png \
  '$imagegen Generate a new website hero image with a similar mood and composition. Do not copy the reference images directly.

Execution instruction: Image 1 is /absolute/path/to/style-reference.jpg. Image 2 is /absolute/path/to/layout-reference.png. Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

## Prompt 規則

核心規則是保留 prompt 原文。

- 不要重寫使用者的圖片 prompt。
- 除非使用者要求，不要翻譯 prompt。
- 不要加入使用者沒有提供的風格細節或限制。
- 除非使用者要求，不要修正文法或拼字。
- 圖片檔案對應與存檔位置只放在 `Execution instruction:`。

## 疑難排解

常見失敗情況之一，是圖片生成後端的網路請求失敗：

```text
image generation failed: network error: error sending request for url (https://chatgpt.com/backend-api/codex/images/generations)
```

這通常代表子 Codex process 無法連線到 ChatGPT 圖片生成後端。請檢查網路連線、VPN 或 proxy 設定、防火牆規則，以及目前已登入的 Codex session 是否能存取 `chatgpt.com`。

## 開發備註

這個 repo 目前沒有 build step。修改文件後，請檢查 Markdown，並確認指令範例仍然使用：

- `npx --yes --package=@openai/codex@0.143.0`
- `codex exec --skip-git-repo-check`
- 對輸入圖片重複使用 `-i /absolute/path`
- 獨立的 `Execution instruction:` 行

## License

目前尚未包含 license file。發布或散佈此 repo 前，請先補上 license。
