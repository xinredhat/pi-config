<!-- markdownlint-disable MD024 -->
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Claude Opus 4.8 model (`claude-opus-4-8`) with native 1M context and 128K max output

### Fixed

- Bump `@anthropic-ai/vertex-sdk` to `^0.22.0` so the `global` region resolves to
  `https://aiplatform.googleapis.com/v1` automatically. The previous `^0.11.4`
  range capped the SDK at 0.11.x, before the global-endpoint fix
  ([anthropics/anthropic-sdk-typescript#800], shipped in vertex-sdk 0.13.0), so
  `GOOGLE_CLOUD_LOCATION=global` produced `https://global-aiplatform.googleapis.com`
  (404) unless the caller set `ANTHROPIC_VERTEX_BASE_URL`. That override is no
  longer required. (`@anthropic-ai/sdk` stays `^0.54.0`; vertex-sdk 0.x keeps the
  `@anthropic-ai/sdk <1` peer, so no API changes.)

  [anthropics/anthropic-sdk-typescript#800]: https://github.com/anthropics/anthropic-sdk-typescript/issues/800
- Replaying cross-provider history (e.g. an OpenAI/Codex session) on a Vertex
  Claude model no longer fails with `400 tool_use.id: String should match
  pattern '^[a-zA-Z0-9_-]+$'`. Composite tool ids such as `call_X|ctc_Y/3` are
  sanitized (illegal chars → `_`) on both `tool_use.id` and the matching
  `tool_result.tool_use_id` so the pair stays linked.
- Replaying cross-provider history no longer fails with `400 Invalid
  \`signature\` in \`thinking\` block`. A `thinkingSignature` that is not a valid
  Anthropic token (e.g. an OpenAI reasoning item `{"id":"rs_...",...}`) now falls
  back to a plain text block instead of being forwarded as a signature; genuine
  Anthropic thinking signatures are still preserved.
- Use `thinking.type=adaptive` and `output_config.effort` for Claude 4.6+
  (Opus 4.6/4.8, Sonnet 4.6). `type=enabled` + `budget_tokens` is deprecated
  on 4.6 and rejected on 4.8. Older models still use extended thinking.
- Honor session thinking `off` (omit thinking params) and `max` (adaptive
  effort `max`, or highest extended budget on 4.5 and older)
- Package-local `createLogger` (`logger.ts`) so npm installs do not import
  repo-only `extensions/shared/logger.ts`

## [0.2.3] - 2026-06-28

### Fixed

- Force `google-auth-library@^10.0.0` via npm overrides to fix OAuth token "Premature close" failure on Node 26

## [0.2.1] - 2026-05-15

### Added

- `ANTHROPIC_VERTEX_PROJECT_ID` environment variable as fallback for project ID resolution

## [0.2.0] - 2026-04-15

### Added

- Claude Sonnet 4.6 model (`claude-sonnet-4-6`)
- 1M context window support via `VERTEX_CLAUDE_1M=true` environment variable
- `-1m` model variants for Claude Opus 4.6 and Sonnet 4.6 with 1M token context window

### Changed

- Updated Claude Opus 4.6 max output tokens from 32K to 128K

## [0.1.3] - 2026-02-07

### Added

- Support for Claude Opus 4.6 model (`claude-opus-4-6`)

## [0.1.2] - 2025-01-30

### Changed

- Further simplified README - removed Features and Common Issues sections
- Cleaner title and structure following Pi extension conventions
- Removed copyright from LICENSE and README

### Removed

- GitHub Actions workflow (tests need peer dependencies)

## [0.1.1] - 2025-01-30

### Changed

- Simplified README - removed unnecessary sections (pricing, fish shell, excessive troubleshooting)
- Cleaner, more focused documentation

## [0.1.0] - 2025-01-30

### Added

- Initial release
- Support for all Vertex AI Claude models (Opus, Sonnet, Haiku)
- Full streaming support
- Extended thinking for reasoning models
- Tool/function calling support
- Image input support
- Prompt caching
- Token usage tracking and cost calculation
- Comprehensive test suite
- NPM and GitHub distribution
