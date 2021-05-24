---
title: "Learning zsh profiles"
last_modified_at: 2021-05-23T22:38:42-05:00
categories:
  - proofs
tags:
  - zsh
---
## Background
I come from the PowerShell world so I understand the concept of having a profile that is sourced in but, for some reason the idea of zsh or bash seems so foriegn. So today I set off to figure it out.

The way I see things .zshrc is the same as Microsoft.PowerShell_profile.ps1. You put a bunch of code in there and it executes it when you start zsh.

So I've gone through an emulated the layout of my PowerShell dotfiles and it looks kind of like this:

~\.zshrc.d
├── exports.zsh  <---------- Export all of my global variables
├── fzf-completions.zsh <--- completions for fzf
├── fzf-keybindings.zsh <--- keybingings for fzf