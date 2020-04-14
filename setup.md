# Setup

This includes any command line setup that will make you as efficient as possible in the exam.

## Bash Configuration

```
vi ~/.bashrc
```

```
alias k=kubectl
alias kn='kubectl config set-context --current --namespace'
```

### Vim Configuration

```
vi ~/.vimrc
```

Sets up vim for good writing of `yaml`
```
set tabstop=2
set expandtab
```

Toggle Numbers

You can toggle line numbers in vim using `:set number` or `:set nonumber` once in.

Copy and Paste

```
Mark lines: Esc+V (then arrow keys)
Copy marked lines: y
Cut marked lines: d
Paste lines: p or P
```

Movement
```
End of line: $
Beginning of line: 0
First non-blank character: ^
Jump forward one line: j
Jump forward ten lines 10j
Jump back one line: k
Jump back ten lines: 10k
Jump to end: G
Jump to beginning: 1G
```

## Tmux

### Installing Tmux

```
sudo apt-get install tmux
```

### Config Setup

Create a tmux config file `vi ~/.tmux.conf`

Include the following useful options:
```
set -g mouse on
```

### Commands

Enter `ctrl+b` followed by subsequent commands in tmux:

```
Split screen horizontally: “
Select screen: Arrow-Keys
Toggle layouts: space
Swap panes: { and }
Detach session: d
List/Select windows: w (left right arrows to select panes)
Scrolling: [ and then use the mouse
```
