# Mise Tool Installation Plan

## Outcome

Use homelab as the source of truth for user CLI and runtime installation across
Linux amd64 and macOS arm64. Mise owns versioned user tools and provides lazy
interactive installation. System-integrated packages remain with the host
package manager.

Do not generate one wrapper script per command. Mise shell integration,
registry metadata, and shims already provide the lazy-loading behavior.

## Ownership

- Homelab owns the inventory, provider, version, eager/lazy mode, and platform.
- The home Ansible role implements generic user-level mise bootstrap and config.
  It must not install system packages or edit system policy.
- Dotfiles owns shell integration and PATH ordering, not a duplicate inventory.
- Host automation owns apt packages and Homebrew formulae or casks.
- Project repositories own local mise config and dependency lockfiles.

## Provider Policy

There is no universal provider order. Select the package class first.

### User CLIs And Runtimes

1. Mise registry name, allowing its registry to select a maintained backend.
2. Mise core for runtimes such as Node, Python, Go, Rust, and Java.
3. Mise Aqua when it supplies checksum, signature, or attestation metadata.
4. Mise GitHub for an official prebuilt release.
5. The upstream project's canonical language backend:
   - pipx for Python CLIs; mise uses uv when uv is installed.
   - cargo for Rust CLIs, preferring cargo-binstall artifacts.
   - go for Go commands.
   - npm for Node CLIs.
6. Host fallback:
   - macOS: Homebrew before Nix.
   - Ubuntu: apt for acceptable versions, otherwise Nix before Linuxbrew.
7. Source builds only when no maintained binary or package exists.

At step 5, follow upstream. Do not arbitrarily prefer Cargo over Go, npm, or
Python.

Mise owns Node itself. npm and npx come from that Node installation; retire nvm
only after project version files and shell behavior pass on both hosts.

Direct gh release downloads are not a separate provider. Use mise's GitHub
backend so version, checksum, provenance, and install path stay together. Do
not add new uses of the deprecated ubi backend.

### System And Desktop Software

Use apt on Ubuntu and Homebrew/casks on macOS for daemons, PAM modules, drivers,
shared libraries, desktop applications, portals, and hardware integration.
Keep targeted Nix ownership for Hyprland and Quickshell where Ubuntu 24.04 is
unsuitable. These packages are eager and never command-not-found installs.

### Local Software

Locally developed tools such as Board stay with their repository build/deploy
workflow until they publish stable cross-platform releases. Mise must not hide
a checkout build behind a second binary with the same name.

## Lazy And Eager Rules

Lazy installation is only for interactive, non-critical tools declared by a
mise registry name with known binary metadata:

- enable not_found_auto_install;
- use mise activate in interactive shells;
- keep the mise shims directory on login and non-interactive PATH;
- a typo must never select or install an undeclared tool.

Install these eagerly during home convergence:

- runtimes required by shell startup, scripts, services, CI, or Ansible;
- raw github, cargo, go, npm, and pipx specs, because an uninstalled raw spec
  has no command-to-tool metadata;
- security, networking, recovery, and deployment commands;
- tools required in SSH or other non-interactive sessions.

Scripts and services use mise exec only for a project-specific environment.
They must not depend on interactive command-not-found hooks.

## Minimal Inventory

Keep one record per logical tool. Do not maintain provider-specific copies.

    tools:
      - name: node
        spec: node@22
        commands: [node, npm, npx]
        install: eager
        platforms: [linux, darwin]

      - name: sipexer
        spec: github:miconda/sipexer@1.2.1
        commands: [sipexer]
        install: eager
        platforms: [linux, darwin]

The commands field validates ownership and duplicates; it does not generate
wrapper scripts. Add backend options only when default asset detection fails.

Pin critical tools. Less critical interactive tools may use a major constraint.
Generate and commit a cross-platform mise.lock. Locked convergence starts only
after Linux amd64 and macOS arm64 artifacts are present.

## Migration Tasks

1. **Inventory current command ownership**
   - Record command, path, provider, version, platform, and runtime consumers.
   - Acceptance: every tool in install vars and macOS docs has one owner.
   - Validation: compare command paths, package ownership, and mise inventory
     on Linux and macOS.

2. **Define the canonical tool manifest**
   - Add the minimal record above under vars/install.
   - Acceptance: one logical tool has one provider and no duplicate lists.
   - Validation: YAML lint and a duplicate command/provider assertion.

3. **Bootstrap mise eagerly**
   - Pin mise before reading the manifest.
   - Use Homebrew on macOS; use the official checksummed release on Ubuntu
     until a suitable distro package exists.
   - Acceptance: a clean host runs mise without preinstalled language runtimes.
   - Validation: clean Linux container and macOS check/bootstrap test.

4. **Render mise config from homelab**
   - Generate the user config and lockfile inputs from the manifest.
   - Acceptance: the current manual Node 22 setting has no second copy.
   - Validation: mise config ls, mise doctor, and Ansible idempotency.

5. **Add shell and SSH activation**
   - Interactive zsh uses mise activate; login and non-interactive shells get
     only the shims PATH.
   - Acceptance: SSH finds eager tools without a desktop-only zsh file.
   - Validation: fresh terminal, zsh login shell, and laptop/Mac SSH checks.

6. **Implement selective convergence**
   - Configure all tools but install only entries marked eager.
   - Acceptance: lazy registry tools install on first interactive use; raw
     backend and operational tools are present after home convergence.
   - Validation: cold-home test, first and second invocation timing, and a typo
     that installs nothing.

7. **Pilot one tool per backend**
   - Candidates: Node/core, a non-critical registry CLI, sipexer/GitHub,
     Hermes/pipx with uv, amtool/Go, and Pi/npm.
   - Acceptance: each tool resolves to the intended provider or records a
     platform exception.
   - Validation: version, executable path, lock entry, and cold invocation.

8. **Add provider and PATH audits**
   - Detect one command supplied by mise plus Brew, Nix, apt, Cargo, Go, uv,
     npm, or ~/bin.
   - Acceptance: convergence reports the conflicting owners and paths.
   - Validation: duplicate fixture and clean Linux/macOS inventories.

9. **Migrate in small batches**
   - Move runtimes, then low-risk CLIs, then agent/deployment tools.
   - Keep the old provider until new path and version checks pass.
   - Acceptance: every batch has a rollback record.
   - Validation: home check/idempotency and representative command smoke.

10. **Document operations and rollback**
    - Cover install, update, lock refresh, offline failure, uninstall, provider
      override, and recovery when mise is unavailable.
    - Acceptance: removing mise from PATH leaves recovery tools and shell
      startup working.
    - Validation: shell startup without mise and one rollback drill.

## Stop Conditions

- Do not migrate GUI, driver, PAM, portal, or daemon packages to mise.
- Do not create per-command lazy wrapper scripts.
- Do not use latest for security or deployment-critical tools.
- Do not remove an old provider before Linux and macOS checks pass.
- Do not enable asdf, vfox, or experimental pkgx without a real requirement.
