---
title: "zsh Aliases"
last_modified_at: 2021-05-23T22:38:42-05:00
categories:
  - crums
tags:
  - zsh
---

### Look through all of the files in the directory and show a preview of it's contents
alias ll="fzf --preview 'bat --color \"always\" --style=numbers --line-range=:500 {}'"
