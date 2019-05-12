---
title: Login and Interactive Shells
tags: [config]
created: '2019-05-11T10:47:11.720Z'
modified: '2019-05-11T10:47:25.490Z'
---

# Login and Interactive Shells

Shells can be invoked in four different modes based on whether it is login or
non-login and interactive or non-interactive.

The [config files](./Shell_Configuration_Files.md) loaded during startup and
shutdown of shells depends on the mode of shell invocation.

## Login shell

A shell being a login shell has got nothing to do with whether a username and
password is requested by the shell. Infact, shells do not handle authentication
at all. Authentication is moslty handled by PAM (Pluggable Authentication
Modules), sshd, su, etc.

Login shells are simply shells started with the option `-l` or `--login` or
shells whose first argument starts with `-`.

Login and non-login shells only differ in the files loaded during startup and
shutdown.

```c
// Example of how to start a login shell without -l flag
// Uses a first argument starting with -
char *const args[] = {"-bash"};
execvp("bash", args);
```

POSIX and `sh` does not have the notion a login shell.

### Checking if shell is a login shell

#### bash

```sh
if [[ shopt -q login_shell ]]; then
    echo 'Login shell'
else
    echo 'Non-login shell'
fi
```

#### zsh

```sh
if [[ -o login ]]; then
    echo 'Login shell'
else
    echo 'Non-login shell'
fi
```

## Interactive shell

A shell is an interactive shell if it is started with the `-i` option or
started without non-option arguments (arguments starting with `-` or `--`)
and without the `-c` option, with both the standard input and standard error
connected to a terminal.

The difference in behaviour between interactive and non-interactive shells
depends on the shell. Behavior in `bash` is explained [here.][bash-interactive]

### Examples:

* `bash script.sh` starts an a **non-interactive** since it has a non-option
argument.

* `bash -i script.sh` and `bash -i -c 'echo I am interactive'` starts an
**interactive** shell because of the `-i` option.

* `bash < inp.txt` and `bash 2> err.txt` starts a **non-interactive** shell
since stdin and stderr, respectively, are not connected to a terminal.

* `bash < inp.txt -i` and `bash 2> err.txt -i` starts an **interactive** shell
because of the `-i` option


### Checking if shell is interactive

#### bash

```sh
if [[ $- == *i* ]]; then
    echo 'Interactive'
else
    echo 'Non-interactive'
fi
```

Due to a [bug][bash-set+i-bug] in `bash` the above method will report that a
shell, started as an interactive, is non-interactive if `set +i` command was
executed in the shell.

`set +i` does not change the interactive behaviour of the shell and so from
`bash 4.4+` the `set +i` returns `bash: set: +i invalid option`

#### zsh

```sh
if [[ $- == *i* ]]; then
    echo 'Interactive'
else
    echo 'Non-interactive'
fi
```

or

```sh
if [[ -o interactive ]]; then
    echo 'Interactive'
else
    echo 'Non-interactive'
fi
```

#### sh

`sh` does not have a [reliable][sh-interactive] way to check if the shell is
interactive.


## Common shell invocations


## Gotchas

* `bash script.sh -i` does not start an interactive shell because the `-i`
is not an option to bash but instead the first argument to `script.sh`.

* `bash -c 'echo I am non-interactive' -i` does not start an interactive shell
because the `-i` is, once again, not an option to bash but instead the first
positional argument provided to the commands specifed by `-c`.

    ```sh
    $ bash -c 'echo My first arg is; echo $0' -i
    My first arg is:
    -i
    ```

* `bash -c "[[ $- == *i* ]] && (echo Interactive || echo Non-interactive)"`
will print `Interactive` when executed from your `bash` console even though
it was invoked with a `-c` flag. This is because of the shell parameter
expansion of `$-` in the interactive console. Quoting the command with `'` will
prevent the parameter expansion and will give the expected output.

    ```sh
    $ bash -c '[[ $- == *i* ]] && (echo Interactive || echo Non-interactive)'
    Non-interactive
    ```

## See also

* [Shell Configuration Files](./Shell_Configuration_Files.md)


## References

1. https://pubs.opengroup.org/onlinepubs/009695399/utilities/sh.html
2. https://linux.die.net/man/1/bash
3. http://zsh.sourceforge.net/Guide/zshguide02.html

[bash-interactive]: https://www.gnu.org/software/bash/manual/html_node/Interactive-Shell-Behavior.html
[sh-interactive]: https://unix.stackexchange.com/questions/26676/how-to-check-if-a-shell-is-login-interactive-batch#comment380621_212065
[bash-set+i-bug]: https://unix.stackexchange.com/a/339517
