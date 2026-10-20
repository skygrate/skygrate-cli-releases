# Skygrate CLI

Install `skygrate`, the command-line tool for Skygrate customers and operators.
This repository only publishes ready-to-run builds; you do not need Python or
any source code.

Each [release](https://github.com/skygrate/skygrate-cli-releases/releases)
contains one archive per platform, a `.sha256` checksum for each archive,
`SHA256SUMS`, `install.sh`, and `stable.txt` with the release's version tag.

## Supported platforms

| Operating system | Processor | Archive |
| --- | --- | --- |
| macOS 13 or later | Apple silicon | `skygrate-darwin-arm64.tar.gz` |
| macOS 13 or later | Intel | `skygrate-darwin-amd64.tar.gz` |
| Linux with glibc (Debian 12+, Ubuntu 22.04+, RHEL 9+) | x86-64 | `skygrate-linux-amd64.tar.gz` |
| Linux with glibc (Debian 12+, Ubuntu 22.04+, RHEL 9+) | ARM64 | `skygrate-linux-arm64.tar.gz` |
| Windows 10 or later, in Git Bash | x86-64 | Not available yet: Windows builds are still being tested |

If a release does not list your platform's archive, that platform is not
available for that version yet. Alpine and other musl-based Linux
distributions are not supported. When Windows builds are released, they will
run in Git Bash ([Git for Windows](https://gitforwindows.org/)); PowerShell and
cmd are not supported.

## Install

### Quick install

In a macOS or Linux terminal:

```bash
curl -fsSL https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/install.sh | sh
```

This installs the latest stable release. The script detects your platform,
downloads the archive, checks its SHA-256 checksum and the program's version,
and installs it under `~/.skygrate`. It puts the `skygrate`
command in `~/.local/bin` (macOS and Linux) or `~/bin` (Git Bash), and prints
the line to add to your shell profile if that directory is not on your `PATH`.
It does not need administrator rights and does not edit your shell profile.

### Install a specific release or a pre-release

Every release has its own installer, which installs exactly that release. Use
it to pin a version, to roll back, or to install a pre-release (pre-releases
are never "latest"). Replace `<tag>` with the tag shown on the
[releases page](https://github.com/skygrate/skygrate-cli-releases/releases), for example `v0.2.0`:

```bash
curl -fsSL https://github.com/skygrate/skygrate-cli-releases/releases/download/<tag>/install.sh | sh
```

### Installer options

Pass options after `sh -s --`, for example
`curl -fsSL https://github.com/skygrate/skygrate-cli-releases/releases/download/<tag>/install.sh | sh -s -- --bin-dir ~/bin`.

| Option | Effect |
| --- | --- |
| `--bin-dir <dir>` | Put the `skygrate` command in another directory. |
| `--version <tag>` | Install another release than the installer's own. |
| `--uninstall` | Sign out, then remove the CLI. Stops without removing anything if sign-out fails. |
| `--keep-session` | With `--uninstall`, remove the CLI without signing out. |

### Manual install on macOS

1. Download the latest release. Use `darwin-arm64` on Apple silicon and
   `darwin-amd64` on Intel.

   ```bash
   VERSION="$(curl -L -s https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/stable.txt)"
   curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-darwin-arm64.tar.gz"
   ```

   `stable.txt` names the latest stable release. To install a specific
   version or a pre-release, set `VERSION` to its tag instead, for example
   `VERSION=v0.2.0`.

2. Validate the archive. Do not skip this step:

   ```bash
   curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-darwin-arm64.tar.gz.sha256"
   shasum -a 256 --check skygrate-darwin-arm64.tar.gz.sha256
   ```

   Continue only if it prints `skygrate-darwin-arm64.tar.gz: OK`. If it
   prints `FAILED`, delete the files, download them again, and do not run the
   program.

3. Extract it and put the command on your `PATH`:

   ```bash
   mkdir -p ~/.skygrate/versions/"${VERSION}" ~/.local/bin
   tar -xzf skygrate-darwin-arm64.tar.gz -C ~/.skygrate/versions/"${VERSION}"
   ln -sfn ~/.skygrate/versions/"${VERSION}"/skygrate/skygrate ~/.local/bin/skygrate
   ```

   Make sure `~/.local/bin` is on your `PATH`, for example
   `echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc`, then open a new
   terminal.

4. Verify the installation:

   ```bash
   skygrate version
   ```

   The first run after installing can take a few seconds while macOS checks
   the new files; later runs start immediately.

5. Clean up:

   ```bash
   rm skygrate-darwin-arm64.tar.gz skygrate-darwin-arm64.tar.gz.sha256
   ```

If you downloaded the archive with a web browser instead of `curl`, macOS may
block the program. Remove the download mark before extracting:
`xattr -d com.apple.quarantine skygrate-darwin-arm64.tar.gz`.

### Manual install on Linux

Use `linux-amd64` on x86-64 and `linux-arm64` on ARM64 (`uname -m` shows
`x86_64` or `aarch64`). For a specific version or a pre-release, set `VERSION`
to its tag. Continue past `sha256sum --check` only if it prints `OK`.

```bash
VERSION="$(curl -L -s https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/stable.txt)"
curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-linux-amd64.tar.gz"
curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-linux-amd64.tar.gz.sha256"
sha256sum --check skygrate-linux-amd64.tar.gz.sha256

mkdir -p ~/.skygrate/versions/"${VERSION}" ~/.local/bin
tar -xzf skygrate-linux-amd64.tar.gz -C ~/.skygrate/versions/"${VERSION}"
ln -sfn ~/.skygrate/versions/"${VERSION}"/skygrate/skygrate ~/.local/bin/skygrate
skygrate version
```

Signing in stores your session in the desktop keyring (GNOME Keyring or
KWallet, through the Secret Service). Servers without a keyring service cannot
store a session yet; `skygrate doctor` reports this.

### Manual install on Windows (Git Bash)

Windows builds are not available yet. These steps apply once a release lists
`skygrate-windows-amd64.tar.gz`. Continue past `sha256sum --check` only if it
prints `OK`.

```bash
VERSION="$(curl -L -s https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/stable.txt)"
curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-windows-amd64.tar.gz"
curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-windows-amd64.tar.gz.sha256"
sha256sum --check skygrate-windows-amd64.tar.gz.sha256

mkdir -p ~/.skygrate/versions/"${VERSION}" ~/bin
tar -xzf skygrate-windows-amd64.tar.gz -C ~/.skygrate/versions/"${VERSION}"
```

Then create the `skygrate` command in `~/bin`. The default Git Bash window
(mintty) needs `winpty` for hidden input, such as pasting the sign-in address;
this launcher uses it only there:

```bash
cat > ~/bin/skygrate <<EOF
#!/bin/sh
if [ "\${TERM_PROGRAM:-}" = mintty ] && [ -t 0 ] && [ -t 1 ] && command -v winpty >/dev/null 2>&1; then
  exec winpty "$HOME/.skygrate/versions/${VERSION}/skygrate/skygrate.exe" "\$@"
fi
exec "$HOME/.skygrate/versions/${VERSION}/skygrate/skygrate.exe" "\$@"
EOF
chmod +x ~/bin/skygrate
skygrate version
```

Git Bash puts `~/bin` on your `PATH`; open a new Git Bash window if
`skygrate` is not found.

## Sign in

```bash
skygrate login --portal https://clientportal.skygrate.click
skygrate whoami
```

## Upgrade

Run the quick install again, or a specific release's installer. It installs
the new version next to the current one and switches to it, keeping the
previous version for rollback. To go back, run the previous release's
installer (`https://github.com/skygrate/skygrate-cli-releases/releases/download/<previous-tag>/install.sh`). With a manual
install, repeat the manual steps for the new version.

## Uninstall

```bash
curl -fsSL https://github.com/skygrate/skygrate-cli-releases/releases/download/<tag>/install.sh | sh -s -- --uninstall
```

Any release's installer can uninstall; use the tag you installed (shown by
`cat ~/.skygrate/current`). This signs you out, then removes `~/.skygrate` and
the `skygrate` command. If signing out fails, for example because the
credential store is locked, nothing is removed; fix that and retry, or add
`--keep-session` to remove the CLI and keep the stored session.
Your settings in `~/.config/skygrate` are kept; delete that directory to
remove them too. After a manual install, run `skygrate logout`, then delete
`~/.skygrate` and the `skygrate` link or launcher.

## Verify every file in a release

`SHA256SUMS` lists the checksum of every archive and `install.sh`. In a
directory with the downloaded files:

```bash
sha256sum --check --ignore-missing SHA256SUMS                 # Linux, Git Bash
shasum -a 256 --check --ignore-missing SHA256SUMS             # macOS
```
