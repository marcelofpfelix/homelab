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

- Shell: `zoxide`, `starship`, `atuin`, `fzf`, `direnv`
- CLI: `gh`, `uv`, `coreutils`, `gum`, `gopass`
- Containers: `colima`, `kubectl`
- Editors: `nvim`
- Sync: `syncthing`
- Security: `pinentry-mac`
