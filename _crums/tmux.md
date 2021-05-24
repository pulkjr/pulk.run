---
title: "All the TMUX Shortcuts"
last_modified_at: 2021-05-23T05:28:36-05:00
categories:
  - crums
tags:
  - tmux
---
## Key bindings
### Search the buffer:
`prefix` + <kbd>[</kbd> = Enter copy mode
`ctrl` + <kbd>s</kbd> = To search (like emacs)

### Install new plugins from git repo and refresh environment
`prefix` + <kbd>I</kbd>

### Update Plugins
`prefix` + <kbd>U</kbd>

### Remove / Uninstall plugins not on the plugin list
`prefix` + <kbd>alt</kbd> + <kbd>u</kbd>
## Panes
### Split Panes horizontaly
`prefix` + <kbd>%</kbd>

### Searching in a tmux pane
```zsh
usage: fzf-tmux [-h HEIGHT[%]] [-w WIDTH[%]] [--] [FZF OPTIONS]
find ~ | fzf-tmux -w 30%
git branch | fzf-tmux -h 15 
```