---
title: "dobare"
last_modified_at: 2021-05-23T22:38:42-05:00
categories:
  - recipes
tags:
  - dotbare
---
[dotbare](https://github.com/kazhala/dotbare) is a command line utility to help manage dotfiles and or as a generic fuzzy git client. 

This process is for a MAC. Follow links for your OS.
### Required dependency

- [fzf](https://github.com/junegunn/fzf) provides the fuzzy logic.

```sh
brew install fzf
```

### Optional dependency

- [tree](https://linux.die.net/man/1/tree) - Provide a directory tree view when finding directory

  ```sh
  brew install tree
  ```

- [bat](https://github.com/sharkdp/bat) - a replacement for cat. Try it you'll see why!

- [delta](https://github.com/dandavison/delta) or [diff-so-fancy](https://github.com/so-fancy/diff-so-fancy)
  or any diff tools of your choice (Fancy git diff preview like in the screen shot)

### To install for oh-my-zsh

1. Clone the repository in to [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh) plugins directory.

  ```sh
  git clone https://github.com/kazhala/dotbare.git $HOME/.oh-my-zsh/custom/plugins/dotbare
  ```

2. Activate the plugin in `~/.zshrc`.

  ```zsh
  plugins=( [plugins...] dotbare [plugins...] )
  ```

3. Then source your `~/.zshrc` file in order to reload it.

### Adding files
It's just a wrapper for git. So all the normal commands