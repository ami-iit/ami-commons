By default, when connecting to a remote server through SSH, the interpreter running in the terminal is not robust to connectivity loss. This means that, if a process was running while the connection is broken, there's no way to reattach to the process from a new SSH connection.

Luckily, this is a longstanding problem that was solved long time ago by tools like [`tmux`](https://github.com/tmux/tmux/wiki). Beyond other useful features it provides a persistent session.

Furthermore there is exist a tool that wrap and extends `tmux` and `screen`, called [`byobu`](https://www.byobu.org/). 

This page provides pointers on the use of [`tmux`](#tmux) and [`byobu`](#byobu).

## tmux

Official documentation:
- https://github.com/tmux/tmux/wiki/Getting-Started

Useful tutorials:
- https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/
- http://tmuxcheatsheet.com/
- https://www.youtube.com/watch?v=DzNmUNvnB04&t=175s

### Tricks

#### Copy text from a tmux session

Tmux has a very particular way to handle splits (terminals part of the view). Mouse, keyboard, and scrollback buffers are treated in a way that could make most of new users puzzled.

Pasting content from the clipboard of the local machine should work OOTB, though copying text from the remote machine does not work.

### Setup
1. First enable mouse support `echo "set -g mouse on" >> $HOME/.tmux.conf`
1. Then, while in a tmux session press `Shift` while using the mouse to select the text to copy. This operation disables the mouse supports and gives back copy capability.

Alternatively, you can use the `tmux` command `copy-mode`: `Ctrl+B [` to enter copy mode, then use the arrow keys to move around and select text by first pressing `Ctrl + spacebar`. Press `Ctrl + w` to copy the selected text to the clipboard and paste it in another `tmux` session using `Ctrl + ]`. In order to copy the context to Linux clipboard, `xclip` will be required, please refer to the [official documentation](http://man.openbsd.org/OpenBSD-current/man1/tmux.1) or the [`tmux` wiki](https://github.com/tmux/tmux/wiki/).

### Usage

~~~bash
# Check existing sessions
tmux ls

# Create a new named session
tmux new -s <name>

# Detach from a session
# Press Ctrl+B D

# Delete a session
tmux kill-session -t <name>

# Reconnect to an existing session
tmux attach -t <name>
~~~

## byobu

Optionally, you can use `byobu` to wrap `tmux` and `screen` and provide a more user-friendly interface.

For an exhaustive tutorial, refer to the [official documentation](https://www.byobu.org/documentation).

### Setup

1. Connect to the target machine through SSH
1. Install the package with `sudo apt install byobu`
1. Disable automatic startup `byobu-disable` (other users of the machine might not like it)
1. Enable mouse support `echo "set -g mouse on" >> $HOME/.byobu/.tmux.conf`

The tmux configuration is huge. This is only the 0.1% of what you can do.

### Usage

~~~bash
# Check existing sessions
byobu list-sessions

# Create a new named session
byobu new -s <name>

# Detach from a session
# Press F6

# Delete a session
byobu kill-session -t <name>

# Reconnect to an existing session (interactively)
byobu-select-session
~~~
