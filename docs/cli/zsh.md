---
title: ZSH
summary: Setup of ZSH
authors:
  - Idar Bergli
date: 2021-04-25
---

So this is all about installing and configuring _ZSH_ on _Ubuntu 20.04 LTS_. But can be used for all ubuntu based installations. We will be using _OH-MY-ZSH_ as well so lets get going.

## Installing ZSH

So lets install the _ZSH_ on the system:

```sh
sudo apt install zsh
```

This will install the latest available _ZSH_ from the package manager. Lets check that it is available:

```sh
zsh --version

zsh 5.8 (aarch64-unknown-linux-gnu)
```

So now lets configure so that ZSH will be the default shell for the system:

```sh
echo $SHELL
chsh -s $(which zsh)
```

Now we its is set as default and to test that logout and in again and see the change.

## OH-MY-ZSH

Lets get some basic tools if they are not all ready installed on to the system:

```sh
sudo apt install curl wget git
```

Then lets install with one of the following commands:

```sh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

or

```sh
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```

### Configure

When installing the _OH-MY-ZSH_ a new `.zshrc` file will be generated where you can configure youre commandline.

Example we add more plugins find the line in `.zshrc` file:

```sh
nano .zshrc
```

Find the line that says `plugins=(git)` and change it to `plugins=(git zsh-autosuggestions zsh-syntax-highlighting)`

then we  need to add those plugsin to _OH-My-ZSH_:

```sh
git clone https://github.com/zsh-users/zsh-autosuggestions.git $ZSH_CUSTOM/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```
