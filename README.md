# Codex Imagegen 0.143.0 Skill

A small Codex skill package for generating and editing bitmap images through a fixed Codex CLI version: `@openai/codex@0.143.0`.

This repository is intentionally narrow. It exists to make image generation behavior repeatable when a workflow needs the older `imagegen` path from Codex `0.143.0`, instead of whatever image tool is available in the current agent session.

Traditional Chinese: [README-zh.md](README-zh.md)

## What This Skill Does

- Runs image generation through `@openai/codex@0.143.0`.
- Supports new image generation, image editing, and reference-image-guided generation.
- Preserves the user's image prompt exactly.
- Passes local input images with absolute `-i` paths.
- Separates operational instructions, such as save location, from the actual image prompt.

## Why We Made This Skill

The main reason for this repository is a network error we encountered in the latest Codex image-generation path:

```text
image generation failed: network error: error sending request for url (https://chatgpt.com/backend-api/codex/images/generations)
```

That error was observed with the latest version path, not with `@openai/codex@0.143.0`.

Codex image-generation behavior can change across versions and runtime surfaces. For teams that need predictable output handling, prompt preservation, or compatibility with a known working image-generation flow, relying on the current session's default image tool can be too loose.

This skill fixes that by delegating image work to a child Codex process pinned to `@openai/codex@0.143.0`.

The main goals are:

1. Keep prompts stable by passing the user's prompt verbatim.
2. Keep image input handling explicit through absolute paths.
3. Keep generated assets easy to find by requiring a clear output path.
4. Keep the workflow portable across repos that use Codex skills.

## Repository Layout

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

## Quick Guide

1. Copy or keep this repository where your Codex skill loader can read it.
2. Ask Codex to use `imagegen-0-143-0` when generating or editing images.
3. Provide a clear image prompt.
4. Provide local image paths when editing or using references.
5. Check the final absolute output path returned by the child Codex process.

Example user request:

```text
Use $imagegen-0-143-0 to generate a polished marketing image of Hong Kong Victoria Harbour at golden hour. Save it to ./generated.
```

## Manual Command

The skill runs this pattern internally:

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  '$imagegen <verbatim user prompt>

Execution instruction: Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

## Example: Generate a New Image

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  '$imagegen Generate a premium product launch hero image for a modern AI automation platform. Use a clean, professional composition with a real workspace, subtle screen glow, and clear room for website headline text.

Execution instruction: Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

## Example: Edit an Existing Image

Use one `-i` argument per input image. Always use absolute paths.

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

## Example: Generate From References

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

## Prompt Rules

The core rule is prompt preservation.

- Do not rewrite the user's image prompt.
- Do not translate the prompt unless the user asks.
- Do not add style details or constraints the user did not provide.
- Do not fix grammar or spelling unless requested.
- Put image-file mapping and save-path instructions only in `Execution instruction:`.

## Troubleshooting

A common failure mode in the latest Codex image-generation path is a network request failure from the image generation backend:

```text
image generation failed: network error: error sending request for url (https://chatgpt.com/backend-api/codex/images/generations)
```

This is the main reason this repository pins image generation to `@openai/codex@0.143.0`. If you still see this error, check network connectivity, VPN or proxy settings, firewall rules, and whether the authenticated Codex session can access `chatgpt.com`.

## Development Notes

There is no build step in this repository. For documentation changes, review the Markdown files and confirm that command examples still use:

- `npx --yes --package=@openai/codex@0.143.0`
- `codex exec --skip-git-repo-check`
- repeated `-i /absolute/path` arguments for input images
- a separate `Execution instruction:` line

## License

No license file is currently included. Add one before distributing or publishing this repository.
