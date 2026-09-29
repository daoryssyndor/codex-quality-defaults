# Codex quality-preserving defaults

A self-contained POSIX shell installer for global Codex instructions and conservative context settings. **The complete AGENTS.md is embedded inside `install.sh`.** No adjacent template, configuration file, download, or model request is needed.

## Install

On Linux, macOS, or WSL, use POSIX `sh` and Python 3.11+ (or Python 3.9+ with `tomli` already installed). Run as your normal user, without sudo:

```sh
sh /path/to/install.sh --dry-run
sh /path/to/install.sh
```

You can clone this repository anywhere or copy only `install.sh` to another machine. Its location does not affect the destination. The installer uses `$CODEX_HOME`, otherwise `~/.codex`. To select another destination:

```sh
sh install.sh --codex-home /path/to/codex-home
```

Minimal containers may also need Python installed; this script does not require Bash or install dependencies automatically.

Restart Codex and start a new session after installation.

## What it changes

- Creates or replaces global `AGENTS.md` with the full embedded instructions: disciplined reasoning, bounded investigations, concise tool output, necessary verification, careful Graft use, and noninteractive Git with a single completed-task commit when authorized.
- Saves and removes a global `AGENTS.override.md`, if present, because it would take precedence over the installed instructions.
- Updates only these top-level configuration keys: `model_auto_compact_token_limit`, `model_auto_compact_token_limit_scope`, `tool_output_token_limit`, and `model_context_window`. The last is removed to use the model's native window.
- Preserves model selection, reasoning effort, permissions, integrations, worker settings, profiles, and other configuration. Missing `config.toml` is created.

For the verified Codex CLI **0.159.0**, an explicit supported model (`gpt-6.1-sol`, `gpt-6-astra`, or `gpt-6-sol`), and fresh matching model metadata, it installs:

```toml
model_auto_compact_token_limit = 240000
model_auto_compact_token_limit_scope = "total"
tool_output_token_limit = 10000
```

This preset requires a 272,000-token native window, 95% effective-context metadata, and a 10,000-token output policy. The verified native compaction calculation is 90% of 272,000 = 244,800, so 240,000 is close to that default. See the [version-pinned Codex model implementation](https://github.com/openai/codex/blob/rust-v0.159.0/codex-rs/protocol/src/openai_models.rs).

For other releases, unknown models, missing/stale metadata, or different limits, it removes these fixed overrides and uses native model defaults. It does not guess a universal context size or silently select a model. Run `--dry-run` to see which choice applies. Existing profiles, project instructions, command-line flags, and managed policies can override global settings.

These are waste-reduction instructions and settings, not a hard token budget. They cannot guarantee a particular subscription saving, identical results, or that total task usage never exceeds a default run. Models cannot reliably predict exact task cost before starting.

## Restore the original files

```sh
sh install.sh --restore-default
```

This restores the **original files saved before the first installation** for the selected Codex home, including previous file contents and modes. Files originally absent are removed. It restores global `AGENTS.md`, `AGENTS.override.md`, and the entire original `config.toml`; later manual edits to these files are therefore replaced, with another backup saved first. It is not a factory reset and does not delete authentication, conversations, or other Codex data.

Backups are stored under `<codex-home>/backups/quality-defaults/`. To restore a specific backup:

```sh
sh install.sh --restore /path/to/backup-directory
```

Use the same `--codex-home` when restoring a custom destination. Backups belong to that destination and are checked for integrity. Keep them private: the original configuration may contain credentials. Do not commit backups or your live Codex configuration to this repository.

## Safety and repeat runs

The installer validates TOML before replacing files, checks that unrelated parsed settings are preserved, saves backups, uses a lock and atomic per-file replacements, and attempts rollback if writing fails. Symlinked managed files are rejected. Repeating installation with the same inputs makes no changes and creates no additional backup; changes in runtime metadata can change the selected settings. The first-install restore point remains unchanged across reinstallations.

Only `install.sh` is required to run. Keep this README beside it for your repository. To update the instruction template in a future release, update the embedded `ETALON_AGENTS` payload in the script; editing a separate AGENTS.md does not change this installer.
