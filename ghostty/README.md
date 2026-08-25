# Ghostty

Reusable Ghostty config: Gruvbox Dark Hard, a solid block cursor, and SSH
terminfo/truecolor handling.

## Install

Copy to one of Ghostty's config paths (both names work depending on version):

```sh
mkdir -p ~/.config/ghostty
cp ghostty/config ~/.config/ghostty/config
cp ghostty/config ~/.config/ghostty/config.ghostty
```

On macOS, Ghostty also reads:

```sh
mkdir -p "$HOME/Library/Application Support/com.mitchellh.ghostty"
cp ghostty/config "$HOME/Library/Application Support/com.mitchellh.ghostty/config"
```

Reload with **Cmd-Shift-,** (macOS) or restart Ghostty.

## Remote SSH color prompt

Ghostty sets `TERM=xterm-ghostty`. Debian/Ubuntu `~/.bashrc` only enables a
color prompt for `xterm-color` and `*-256color`, so a plain SSH session looks
wrong until tmux remaps `TERM` to `tmux-256color`.

On the remote host, extend the prompt case and re-export `COLORTERM` (sshd
typically `AcceptEnv`s only `LANG` / `LC_*`):

```bash
case "$TERM" in
    xterm-color|*-256color|xterm-ghostty|ghostty) color_prompt=yes;;
esac

case "$TERM" in
    xterm*|tmux*|screen*|ghostty)
        export COLORTERM="${COLORTERM:-truecolor}"
        ;;
esac
```
