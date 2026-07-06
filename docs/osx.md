# macOS Setup Notes

These notes are intentionally generic. Keep personal tokens, usernames, and
machine-specific values in private inventory or a private notes repository.

## Requirements

- Create the target user before running host setup.
- Enable remote access when managing the host over SSH.
- Install Xcode command line tools so Python and fact gathering work correctly.

## Shell

Homebrew shell setup:

```sh
echo 'eval "$(/opt/homebrew/bin/brew shellenv zsh)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv zsh)"
```

## System

Prevent sleep on managed desktop hosts:

```sh
sudo pmset -a displaysleep 0 sleep 0 disksleep 0 networkoversleep 1
```

## Packages

Useful package groups to model in Ansible:

- Shell: `tmux`, `zoxide`, `starship`, `atuin`, `fzf`, `eza`, `direnv`,
  `television`, `tmuxup`
- CLI: `gh`, `uv`, `coreutils`, `gum`, `gopass`, `iproute2mac`, `ansible`,
  `beads`, `task`, `taskwarrior-tui`
- Containers: `colima`, `kubectl`
- Editors: `nvim`
- Sync: `syncthing`
- Security: `pinentry-mac`

Additional package sources to evaluate:

- Homebrew taps: `hashicorp/tap`, `sielicki/dystemctl`
- Homebrew packages from taps: `hashicorp/tap/vault`
- Homebrew casks: `font-fira-code-nerd-font`
- Tentative casks: `codex`; npm may remain the working install path
- Node tools: `clawdbot`, `claude-code`, `clawdhub`, `@telnyx/api-cli`
- Go tools: `github.com/prometheus/alertmanager/cmd/amtool@latest`
- Git tools: `romkatv/zsh-defer` under `/opt/git/romkatv/zsh-defer`

Runtime setup notes:

- Install Node from the repository `.nvmrc` and run `nvm use`.
- Split shell profile snippets that differ between Linux and Darwin.
- Ensure `/opt/homebrew/bin` is available before managed shell tools run.
