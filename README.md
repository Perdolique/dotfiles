# Dotfiles

Public, cross-platform dotfiles managed with [chezmoi](https://www.chezmoi.io/). The initial setup covers Zsh on macOS and Linux.

Secrets, work-specific commands, and machine-specific overrides are deliberately kept outside this repository in `~/.config/zsh/local.zsh` or `$XDG_CONFIG_HOME/zsh/local.zsh`.

## Prerequisites

- Git
- Zsh
- chezmoi

On macOS, install chezmoi with Homebrew:

```sh
brew install chezmoi
```

On Linux, use the system package manager or the [official chezmoi installation instructions](https://www.chezmoi.io/install/).

## Existing machine setup

Initialize the repository without applying changes:

```sh
chezmoi init Perdolique
```

Create the private local configuration before applying the public files:

```sh
local_zsh_dir="${XDG_CONFIG_HOME:-$HOME/.config}/zsh"
mkdir -p "$local_zsh_dir"
touch "$local_zsh_dir/local.zsh"
chmod 600 "$local_zsh_dir/local.zsh"
```

Put secrets, work-specific functions, and machine-specific overrides in that file. For example:

```sh
export CHROMATIC_PROJECT_TOKEN=""
export CROWDIN_API_TOKEN=""
```

Never add `local.zsh` to chezmoi or Git.

If Oh My Zsh is already installed, move it to a recoverable backup before the first apply:

```sh
mv "$HOME/.oh-my-zsh" "$HOME/.oh-my-zsh.pre-chezmoi"
```

Review and apply the managed changes:

```sh
chezmoi diff
chezmoi apply -v
```

Keep the backup until the new shell setup has been verified.

## Fresh machine setup

The one-command setup is intended only for a fresh machine without existing dotfiles:

```sh
sh -c "$(curl -fsLS https://get.chezmoi.io)" -- init --apply Perdolique
```

Create the private local configuration afterwards if the machine needs secrets or overrides.

## Managed Zsh behavior

- Oh My Zsh with its built-in `git` plugin
- Oh My Zsh cache outside its exact chezmoi-managed installation
- A compact prompt with the current Git branch
- Portable paths for pnpm, Homebrew, OpenJDK, MySQL client, .NET, LM Studio, Cargo, and Vite+
- Conditional completions for Google Cloud CLI, Heroku CLI, and Mole
- Python 3 aliases
- `git-delete-merged` for deleting local branches already merged into the remote default branch

Optional tools are initialized only when their files or executables are available.

## Local configuration

The public `.zshrc` sources the XDG path when `XDG_CONFIG_HOME` is set and otherwise uses the default path:

```text
$XDG_CONFIG_HOME/zsh/local.zsh
~/.config/zsh/local.zsh
```

The file should remain untracked with mode `0600`. It is the appropriate place for API tokens, employer-specific database helpers, and per-machine overrides.

## Daily workflow

Edit a managed file and apply it:

```sh
chezmoi edit --apply ~/.zshrc
```

Review pending changes:

```sh
chezmoi status
chezmoi diff
```

Pull and apply updates from the repository:

```sh
chezmoi update -v
```

Automatic commits and pushes are intentionally not enabled.

## Updating Oh My Zsh

Oh My Zsh is installed as an exact chezmoi archive pinned to a specific upstream commit. Its own updater is disabled so shell behavior changes only through reviewed dotfiles commits.

To update it:

1. Resolve the desired upstream commit from `ohmyzsh/ohmyzsh`.
2. Replace the commit in `home/.chezmoiexternal.toml`.
3. Run `chezmoi diff` and `chezmoi apply -v`.
4. Start a new login shell and verify the prompt and Git aliases.
5. Commit the pin update.

## License

[The Unlicense](./LICENSE)
