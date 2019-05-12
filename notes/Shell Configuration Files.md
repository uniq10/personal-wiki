---
title: Shell Configuration Files
created: '2019-05-10T09:17:55.571Z'
modified: '2019-05-11T10:46:13.295Z'
tags: [bash, config, posix, sh, shell, zsh]
---

# Shell Configuration Files

Only the common invocation types of shells are covered here. For other shell
invocation options check the references.

## posix

The following config files are loaded in order based on the type of shell
invocation:

* The file pointed to by`$ENV`
  * **All interactive shells**

In all **non-interactive shells**, no config files are loaded.

## sh

The following config files are loaded in order based on the type of shell
invocation:

* The file pointed to by`$ENV`
  * **All interactive shells**

* `/etc/profile`
  * **All login shells**

* `~/.profile`
  * **All login shells**

When started as a **non-interactive non-login shell**, no config files are
loaded.

## bash

The following config files are loaded in order based on the type of shell
invocation:

* The file pointed to by `$BASH_ENV`
  * **All non-interactive shells**

* `/etc/profile`
  * **All login shells**

* `~/.bash_profile`
  * **All login shells**

* `~/.bash_login` when `~/.bash_profile` not readable/exist
  * **All login shells**

* `~/.profile` when `~/.bash_profile` and `~/.bash_login` not readable/exist
  * **All login shells**

* `~/.bashrc`
  * **Interactive non-login shell**
  * When bash is able to determine that its **standard input is connected to a
  network (sshd, etc.)**

On all **login bash shell exits**, commands in `~/.bash_logout` and
`/etc/bash.bash_logout` are executed in order.

## zsh

The following config files are loaded in order based on the type of shell
invocation:

* `/etc/zshenv`
  * **All shells**

* `$ZDOTDIR/.zshenv` when `RCS` option is set
  * **All shells**

* `/etc/zprofile` when `RCS` and `GLOBAL_RCS` options are set
  * **All login shells**

* `$ZDOTDIR/.zprofile` when `RCS` option is set
  * **All login shells**

* `/etc/zshrc` when `RCS` and `GLOBAL_RCS` options are set
  * **All interactive shells**

* `$ZDOTDIR/.zshrc` when `RCS` option is set
  * **All interactive shells**

* `/etc/zlogin` when `RCS` and `GLOBAL_RCS` options are set
  * **All login shells**

* `$ZDOTDIR/.zlogin` when `RCS` option is set
  * **All login shells**


On all **login zsh shell exits**, the commands in the following files are
executed in order:

* `$ZDOTDIR/.zlogout` if `RCS` option is set
* `/etc/zlogout` if `RCS` and `GLOBAL_RCS` options are set


`RCS` and `GLOBAL_RCS` options are set by default but the config files can
set/unset the options in them and this affects the loading of the subsequent
config files.

If `$ZDOTDIR` is unset, then `$HOME` is used instead.

## See also

* [Login and Interactive shells](./Login and Interactive Shells.md)

## References

1. https://pubs.opengroup.org/onlinepubs/009695399/utilities/sh.html
2. https://linux.die.net/man/1/bash
3. https://linux.die.net/man/1/zsh
