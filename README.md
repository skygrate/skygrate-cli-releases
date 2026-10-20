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
| Windows 10 or later, in Git Bash | x86-64 | `skygrate-windows-amd64.tar.gz` |

If a release does not list your platform's archive, that platform is not
available for that version yet. Alpine and other musl-based Linux
distributions are not supported. On Windows, use Git Bash
([Git for Windows](https://gitforwindows.org/)); PowerShell and cmd are not
supported.

## Install

### Quick install

In a macOS or Linux terminal, or in Git Bash on Windows:

```bash
curl -fsSL https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/install.sh | sh
```

The script detects your platform, downloads the latest archive, checks its
SHA-256 checksum, and installs it under `~/.skygrate`. It puts the `skygrate`
command in `~/.local/bin` (macOS and Linux) or `~/bin` (Git Bash), and prints
the line to add to your shell profile if that directory is not on your `PATH`.
It does not need administrator rights and does not edit your shell profile.

Options, passed after `sh -s --`:

```bash
curl -fsSL https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/install.sh | sh -s -- --version v0.1.0
```

| Option | Effect |
| --- | --- |
| `--version <tag>` | Install a specific release instead of the latest. |
| `--bin-dir <dir>` | Put the `skygrate` command in another directory. |
| `--uninstall` | Remove the CLI. Signs you out first unless `--keep-session` is given. |

### Manual install on macOS

1. Download the latest release. Use `darwin-arm64` on Apple silicon and
   `darwin-amd64` on Intel.

   ```bash
   VERSION="$(curl -L -s https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/stable.txt)"
   curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-darwin-arm64.tar.gz"
   ```

   To install a specific version, set `VERSION` to its tag instead, for
   example `VERSION=v0.1.0`.

2. Validate the archive (optional, recommended):

   ```bash
   curl -LO "https://github.com/skygrate/skygrate-cli-releases/releases/download/${VERSION}/skygrate-darwin-arm64.tar.gz.sha256"
   shasum -a 256 --check skygrate-darwin-arm64.tar.gz.sha256
   ```

   A valid archive prints `skygrate-darwin-arm64.tar.gz: OK`. If it prints
   `FAILED`, delete the files and download them again.

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
`x86_64` or `aarch64`).

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

Run the quick install again; it installs the latest release next to the
current one and switches to it, keeping the previous version for rollback.
To go back, install the previous tag with `--version`. With a manual install,
repeat the manual steps for the new version.

## Uninstall

```bash
curl -fsSL https://github.com/skygrate/skygrate-cli-releases/releases/latest/download/install.sh | sh -s -- --uninstall
```

This signs you out, then removes `~/.skygrate` and the `skygrate` command.
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
