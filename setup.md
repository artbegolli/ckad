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
Past lines: p or P
```

## Tmux

Enter `ctrl+b` followed by subsequent commands in tmux:

```
Split screen horizontally: “
Select screen: Arrow-Keys
Toggle layouts: space
Swap panes: { and }
Detach session: d
List/Select windows: w (left right arrows to select panes)
```

For scrolling you have two options:

Set mouse scroll:

`ctrl+b` followed by `:` and then
```
setw -g mouse on

or

setw -g mode-mouse on
```

Alternatively you can scroll by 

`ctrl+b` followed by `[`

