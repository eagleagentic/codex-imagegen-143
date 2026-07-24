---
name: imagegen-0-143-0
description: "Generate, edit, modify, or transform bitmap images by delegating to the imagegen skill inside @openai/codex 0.143.0. Use when a Codex user asks for AI image generation, image editing, image modification, visual variants, reference-image-guided generation, local asset creation, marketing visuals, product imagery, photorealistic scenes, illustrations, or other raster image outputs and specifically needs the Codex 0.143.0 imagegen path."
---

# Imagegen 0.143.0

Use this skill to run image generation or image editing through a fixed Codex CLI version:
`@openai/codex@0.143.0`.

Do not call the current session's built-in `image_gen` tool for this skill. Delegate to a child Codex process with `$imagegen` in the prompt.

## Workflow

1. Clarify only if the request is missing a required subject, target image, or output destination.
2. Create the destination directory before running the command. Default to `./generated` when the user does not specify a path.
3. For local input/reference/edit images, resolve each file to an absolute path first, then pass it with `-i <absolute-path>`. Do not use relative paths for image inputs.
4. Put the user's image prompt after `$imagegen` verbatim. Do not rewrite, summarize, translate, expand, restructure, or "improve" the user's prompt.
5. Add only execution instructions that are not part of the image prompt, such as save location and final response format. Keep them in a separate `Execution instruction:` sentence after the verbatim prompt.
6. Report the final path emitted by the child process.

Run the command directly:

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  '$imagegen <verbatim user prompt>

Execution instruction: Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

With input images:

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  -i /absolute/path/to/assets/source.png \
  -i /absolute/path/to/assets/style-reference.jpg \
  '$imagegen <verbatim user prompt>

Execution instruction: Image 1 is /absolute/path/to/assets/source.png. Image 2 is /absolute/path/to/assets/style-reference.jpg. Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

Attach assets by adding one `-i <absolute-path>` argument per image before the prompt:

```bash
npx --yes --package=@openai/codex@0.143.0 -- \
  codex exec \
  --skip-git-repo-check \
  -m gpt-5.5 \
  -c 'model_reasoning_effort="medium"' \
  -i /absolute/path/to/input.png \
  -i /absolute/path/to/reference.jpg \
  '$imagegen <verbatim user prompt>

Execution instruction: Image 1 is /absolute/path/to/input.png. Image 2 is /absolute/path/to/reference.jpg. Save the final image to ./generated and output its absolute file path directly. No need to review and verify.'
```

## Prompt Preservation

Preserve the user's prompt exactly:

- Do not normalize wording.
- Do not add creative details.
- Do not convert the prompt into a schema.
- Do not add asset-type labels, taxonomy, style notes, or constraints that the user did not write.
- Do not fix grammar or spelling unless the user explicitly asks.
- If input image roles are not already stated by the user, describe only the absolute file-to-image mapping in `Execution instruction:` and leave the image prompt unchanged.

## Reference

Read `references/usage.md` when you need examples for output paths, asset inputs, or command assembly.
