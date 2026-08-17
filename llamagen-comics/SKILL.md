---
name: llamagen-comics
description: Create, inspect, continue, and update AI comics through the official LlamaGen CLI and Comic API. Use when a user asks to generate a comic or manga from a prompt or source URL, wait for or check a comic generation, continue an existing comic, regenerate a panel, inspect Comic API usage, authenticate LlamaGen from a browser, or configure LlamaGen test and production backends.
---

# LlamaGen Comics

Use `@llamagen/cli` as the only command-line integration. Keep authentication interactive, keep credentials out of output, and return finished comic results rather than internal authorization details.

## Bootstrap

1. Check whether `llamagen` is available:

   ```bash
   llamagen version
   ```

2. If it is missing, install the official CLI:

   ```bash
   npm install --global @llamagen/cli
   ```

3. Check authentication:

   ```bash
   llamagen auth status
   ```

4. If authentication is missing or invalid, run the browser flow:

   ```bash
   llamagen auth login
   ```

   Tell the user to approve the terminal in the browser if interaction is required. Do not ask the user to paste a browser cookie or Comic API token. Do not read browser storage. The browser returns a one-time code; the CLI stores the resulting credential locally.

For a custom environment, keep the browser site and Comic API independently configurable:

```bash
llamagen config set site-url https://staging.example.com
llamagen config set api-url https://api.staging.example.com
```

Use `LLAMAGEN_SITE_URL`, `LLAMAGEN_API_URL`, and `LLAMAGEN_API_KEY` only for explicit automation or CI requirements. Never print their values.

## Create a comic

Require either a story prompt or `promptUrl`. Ask one concise question only when both are missing. Use `--wait` by default so the result is useful in the same task.

```bash
llamagen comic create \
  --prompt "A detective follows a glowing paper crane through rainy Shanghai" \
  --style manga \
  --fix-panel-num 4 \
  --wait
```

Supported creation inputs include:

- `--prompt <text>` for the story or panel directions.
- `--prompt-url <url>` for a source document or uploaded script.
- `--style <style>` and `--preset <preset-id>` when the user supplies them.
- `--size <width>x<height>` for output dimensions.
- `--language <language>` for comic text.
- `--fix-panel-num <1-20>` for a single-page fixed panel count.
- `--wait` to poll until a terminal status.

Do not invent a preset ID. Preserve the user's language and creative constraints. Quote prompts as one shell argument.

If the user explicitly wants asynchronous submission, omit `--wait`, return the generation ID, and explain how to inspect it.

## Inspect a generation

```bash
llamagen comic get <generation_id>
```

Treat `PROCESSED`, `COMPLETED`, and `SUCCEEDED` as success. Treat `FAILED`, `CANCELED`, and `CANCELLED` as terminal failures. For a finished result, return the useful image or asset URLs and the generation ID. Avoid dumping unrelated raw response fields unless the user requests JSON details.

## Continue a comic

Require the existing generation ID and new story direction:

```bash
llamagen comic continue <generation_id> \
  --prompt "Continue with the detective discovering who folded the crane"
```

After submission, inspect the generation with `llamagen comic get <generation_id>` when the command response does not already contain a final result.

## Regenerate a panel

Require the generation ID, page number, panel number, and replacement direction:

```bash
llamagen comic update-panel <generation_id> \
  --page 1 \
  --panel 3 \
  --prompt "Make the alley brighter and keep the detective's coat consistent"
```

Use `--panel-prompt` when the user explicitly provides a panel-specific prompt. Do not guess page or panel numbers.

## Inspect usage

```bash
llamagen comic usage
```

Summarize remaining or total credits when present. Do not expose the credential or authorization header.

## Sign out

```bash
llamagen auth logout
```

Explain that logout removes the local CLI credential only. It does not sign the browser out or revoke the account-wide Comic API token. If `LLAMAGEN_API_KEY` is set, the process may remain authenticated.

## Failure handling

- Authentication missing or rejected: run `llamagen auth login` once, then retry the original operation.
- API unreachable: show the configured API hostname and suggest checking `llamagen config get api-url`; never show a token.
- Custom login endpoint unavailable: inspect `llamagen config get site-url`, then restore the production default with `llamagen config unset site-url` when appropriate.
- Invalid generation or panel input: report the exact ID or numeric argument that needs correction.
- Timeout: preserve the generation ID and use `llamagen comic get` rather than resubmitting and charging twice.
- Unknown CLI behavior: run the relevant command with `help` before constructing unsupported flags.

## Safety rules

- Never request, copy, log, or transmit a LlamaGen browser Cookie.
- Never print a full API token, credential file, or authorization header.
- Never commit `~/.llamagen/credentials.json` to a repository.
- Do not create a duplicate generation merely because polling timed out.
- Ask before using a paid generation when the user's intent is exploratory or ambiguous.
