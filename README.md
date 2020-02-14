[![Financial Contributors on Open Collective](https://opencollective.com/zplugin/all/badge.svg?label=financial+contributors)](https://opencollective.com/zplugin) [![paypal](https://img.shields.io/badge/-Donate-yellow.svg?longCache=true&style=for-the-badge)](https://www.paypal.me/ZdharmaInitiative)
[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=D54B3S7C6HGME)
[![patreon](https://img.shields.io/badge/-Patreon-orange.svg?longCache=true&style=for-the-badge)](https://www.patreon.com/psprint)

[![Status][status-badge]][status-link] [![MIT License][MIT-badge]][MIT-link] [![][ver-badge]][ver-link] ![][act-badge] [![Join the chat at https://gitter.im/zdharma/zinit][gitter-badge]][gitter-link]

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [News](#news)
- [Zinit](#zinit)
- [Zinit Wiki](#zinit-wiki)
- [Installation](#installation)
  - [Option 1 - Automatic Installation (Recommended)](#option-1---automatic-installation-recommended)
  - [Option 2 - Manual Installation](#option-2---manual-installation)
- [Usage](#usage)
  - [Introduction](#introduction)
  - [Example Usage](#example-usage)
  - [Ice Modifiers](#ice-modifiers)
  - [Zinit Commands](#zinit-commands)
  - [Updating Zinit and Plugins](#updating-zinit-and-plugins)
  - [Using Oh My Zsh Themes](#using-oh-my-zsh-themes)
- [Completions](#completions)
  - [Calling `compinit` Without Turbo Mode](#calling-compinit-without-turbo-mode)
  - [Calling `compinit` With Turbo Mode](#calling-compinit-with-turbo-mode)
  - [Ignoring Compdefs](#ignoring-compdefs)
  - [Disabling System-Wide `compinit` Call (Ubuntu)](#disabling-system-wide-compinit-call-ubuntu)
- [Zinit Module](#zinit-module)
  - [Motivation](#motivation)
  - [Installation](#installation-1)
  - [Measuring Time of `source`s](#measuring-time-of-sources)
  - [Debugging](#debugging)
- [Hints and Tips](#hints-and-tips)
  - [Customizing Paths](#customizing-paths)
  - [Non-GitHub (Local) Plugins](#non-github-local-plugins)
  - [Extending Git](#extending-git)
  - [Preinstalling Plugins](#preinstalling-plugins)
- [Getting Help and Community](#getting-help-and-community)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# News

<details>
  <summary>Here are the new features and updates added to Zinit in the last 90 days.</summary>

* 09-02-2020

  Note that the ice `extract` can handle files with spaces – to encode such a name use
  the non-breaking space (Right Alt + Space) in place of the in-filename spaces :).

* 07-02-2020
  - A new ice `extract` which extracts:
    * all files with recognized archive extensions like `zip`, `tar.gz`, etc.,
    * if no such files will be found, then: all files with recognized archive
      **types** (examined with the `file` command),
    * OR, IF GIVEN – the given files, e.g.: `extract'file1.zip file2.tgz'`,
    * the automatic searching for archives ignores files in sub-sub-directories and
      located deeper,
  - It has a `!` flag – i.e.: `extract'!…'` – it'll cause the files to be moved
    one directory-level up upon unpacking,
  - and also a `-` flag – i.e.: `extract'-…'` – it'll prevent removal of the archive
    after unpacking; useful to allow comparing timestamps with the server in case of
    snippet-downloaded file,
  - the flags can be combined, e.g.: `extract'!-'`,
  - also, the function `ziextract` has a new option `--auto`, which causes the
    automatic behavior identical to the empty `extract` ice.
* 21-01-2020
  - A few tips for the project rename following the field reports (the issues created
    by users):
    - the `ZPLGM` hash is now `ZINIT`,
    - the annexes are moved under [zinit-zsh](https://github.com/zinit-zsh)
      organization.

* 19-01-2020
  - The name has been changed to **Zinit** based on the results of the
    [poll](https://github.com/zdharma/zinit/issues/235).
  - In general, you don't have to do anything after the name change.
  - Only a run of `zinit update --all` might be necessary.
  - You might also want to rename your `zplugin` calls in `zshrc` to `zinit`.
  - Zinit will reuse `~/.zplugin` directory if it exists, otherwise it'll create
    `~/.zinit`.

* 15-01-2020
  - There's a new function, `ziextract`, which unpacks the given file. It supports many
    formats (notably also `dmg` images) – if there's a format that's unsupported please
    don't hesitate to [make a
    request](https://github.com/zdharma/zinit/issues/new?template=feature_request.md)
    for it to be added. A few facts:
    - the function is available only at the time of the plugin/snippet installation,
    - it's to be used within `atclone` and `atpull` ices,
    - it has an optional `--move` option which moves all the files from a subdirectory
      up one level,
    - one other option `--norm` prevents the archive from being deleted upon unpacking.
  - snippets now aren't re-downloaded unless they're newer on the HTTP server; use
    this with the `--norm` option of `ziextract` to prevent unnecessary updates; for
    example, the [firefox-dev package](https://github.com/Zsh-Packages/firefox-dev)
    uses this option for this purpose,
  - GitHub doesn't report proper `Last-Modified` HTTP server for the files in the
    repositories so the feature doesn't yet work with such files.

* 13-12-2019
  - The packages have been disconnected from NPM registry and now live only on Zsh
    Packages organization. Publishing to NPM isn't needed.
  - There are two interesting packages,
    [any-gem](https://github.com/Zsh-Packages/any-gem) and
    [any-node](https://github.com/Zsh-Packages/any-node). They allow to install any
    Gem(s) or Node module(s) locally in a newly created plugin directory. For example:

    ```zsh
    zinit pack param='GEM -> rails' for any-gem
    zinit pack param='MOD -> doctoc' for any-node
    # To have the command in zshrc, add an id-as'' ice so that
    # Zinit knows that the package is already installed
    # (also: the Unicode arrow is allowed)
    zinit id-as=jekyll pack param='GEM → jekyll' for any-gem
    ```

    The binaries will be exposed without altering the PATH via shims
    ([Bin-Gem-Node](https://github.com/zinit-zsh/z-a-bin-gem-node) annex is needed).
    Shims are correctly removed when deleting a plugin with `zinit delete …`.

* 11-12-2019
  - Zinit now supports installing special-Zsh NPM packages! Bye-bye the long and
    complex ice-lists! Check out the
    [Wiki](http://zdharma.org/zinit/wiki/NPM-Packages/) for an introductory document
    on the feature.

* 25-11-2019
  - A new subcommand `run` that executes a command in the given plugin's directory. It
    has an `-l` option that will reuse the previously provided plugin. So that it's
    possible to do:

    ```zsh
    zplg run my/plugin ls
    zplg run -l cat \*.plugin.zsh
    zplg run -l pwd
    ```

* 07-11-2019
  - Added a prefix-char: `@` that can be used before plugins if their name collides
    with one of the ice-names. For example `sharkdp/fd` collides with the `sh` ice
    (which causes the plugin to be loaded with the POSIX `sh` emulation applied). To
    load it, do e.g.:

    ```zsh
    zinit as"null" wait"2" lucid from"gh-r" for \
        mv"exa* -> exa" sbin"exa"  ogham/exa \
        mv"fd* -> fd" sbin"fd/fd"  @sharkdp/fd \
        sbin"fzf" junegunn/fzf-bin
    ```

    i.e.: precede the plugin name with `@`. Note: `sbin''` is an ice added by the
    [z-a-bin-gem-node](https://github.com/zinit/z-a-bin-gem-node) annex, it provides
    the command to the command line without altering `$PATH`.

    See the [Zinit Wiki](http://zdharma.org/zinit/wiki/For-Syntax/) for more
    information on the for-syntax.

* 06-11-2019
  - A new syntax, called for-syntax. Example:

    ```zsh
     zinit as"program" atload'print Hi!' for \
         atinit'print First!' zdharma/null \
         atinit'print Second!' svn OMZ::plugins/git
    ```

    The output:

    ```
    First!
    Hi!
    Second!
    Hi!
    ```

    And also:

    ```zsh
    % print -rl $path | egrep -i '(/git|null)'
    /root/.zinit/snippets/OMZ::plugins/git
    /root/.zinit/plugins/zdharma---null
    ```

    To load in light mode, use a new `light-mode` ice. More examples and information
    can be found on the [Zinit Wiki](http://zdharma.org/zinit/wiki/For-Syntax/).

* 03-11-2019
  - A new value for the `as''` ice – `null`. Specifying `as"null"` is like specifying
    `pick"/dev/null" nocompletions`, i.e.: it disables the sourcing of the default
    script file of a plugin or snippet and also disables the installation of
    completions.

* 30-10-2019
  - A new ice `trigger-load''` – create a function that loads given plugin/snippet,
    with an option (to use it, precede the ice content with `!`) to automatically
    forward the call afterwards. Example use:

    ```zsh
    # Invoking the command `crasis' will load the plugin that
    # provides the function `crasis', and it will be then
    # immediately invoked with the same arguments
    zinit ice trigger-load'!crasis'
    zinit load zdharma/zinit-crasis
    ```

* 22-10-2019
  - A new ice `countdown` – causes an interruptable (by Ctrl-C) countdown 5…4…3…2…1…0
    to be displayed before running the `atclone''`, `atpull''` and `make` ices.

* 21-10-2019
  - The `times` command has a new option `-m` – it shows the **moments** of the plugin
    load times – i.e.: how late after loading Zinit a plugin has been loaded.

* 20-10-2019
  - The `zinit` completion now completes also snippets! The command `snippet`, but
    also `delete`, `recall`, `edit`, `cd`, etc. all receive such completing.
  - The `ice` subcommand can now be skipped – just pass in the ices, e.g.:
    ```zsh
    zinit atload"zicompinit; zicdreplay" blockf
    zinit light zsh-users/zsh-completions
    ```
  - The `compile` command is able to compile snippets.
  - The plugins that add their subdirectories into `$fpath` can be now `blockf`-ed –
    the functions located in the dirs will be correctly auto-loaded.

* 12-10-2019
  - Special value for the `id-as''` ice – `auto`. It sets the plugin/snippet ID
    automatically to the last component of its spec, e.g.:

    ```zsh
    zinit ice id-as"auto"
    zinit load robobenklein/zinc
    ```

    will load the plugin as `id-as'zinc'`.

* 14-09-2019
  - There's a Vim plugin which extends syntax highlighting of zsh scripts with coloring
    of the Zinit commands. [Project
    homepage](https://github.com/zinit/zinit-vim-syntax).

* 13-09-2019
  - New ice `aliases` which loads plugin with the aliases mechanism enabled. Use for
    plugins that define **and use** aliases in their scripts.

* 11-09-2019
  - New ice-mods `sh`,`bash`,`ksh`,`csh` that load plugins (and snippets) with the
    **sticky emulation** feature of Zsh – all functions defined within the plugin will
    automatically switch to the desired emulation mode before executing and switch back
    thereafter. In other words it is now possible to load e.g. bash plugins with
    Zinit, provided that the emulation level done by Zsh is sufficient, e.g.:

    ```zsh
    zinit ice bash pick"bash_it.sh" \
            atinit"BASH_IT=${ZINIT[PLUGINS_DIR]}/Bash-it---bash-it" \
            atclone"yes n | ./install.sh"
    zinit load Bash-it/bash-it
    ```

    This script loads correctly thanks to the emulation, however it isn't functional
    because it uses `type -t …` to check if a function exists.

* 10-09-2019
  - A new ice-mod `reset''` that ivokes `git reset --hard` (or the provided command)
    before `git pull` and `atpull''` ice. It can be used it to implement altering (i.e.
    patching) of the plugin's files inside the `atpull''` ice – `git` will report no
    conflicts when doing `pull`, and the changes can be then again introduced by the
    `atpull''` ice.
  - Three new Zinit annexes (i.e.
    [extensions](http://zdharma.org/zinit/wiki/Annexes/)):

      - [z-a-man](https://github.com/zinit/z-a-man)

        Generates man pages and code-documentation man pages from plugin's README.md
        and source files (the code documentation is obtained from
        [Zshelldoc](https://github.com/zdharma/zshelldoc)).

      - [z-a-test](https://github.com/zinit/z-a-test)

        Runs tests (if detected `test` target in a `Makefile` or any `*.zunit` files)
        on plugin installation and non-empty update.

      - [z-a-patch-dl](https://github.com/zinit/z-a-patch-dl)

        Allows easy download and applying of patches, to e.g. aid building a binary
        program equipped in the plugin.

  - A new variable is being recognized by the installation script:
    `$ZPLG_BIN_DIR_NAME`. It configures the directory within `$ZPLG_HOME` to which
    Zinit should be cloned.

</details>

To see the full history check [the changelog](CHANGELOG.md).

# Zinit

<p align="center">
<a href="https://github.com/zdharma/pm-perf-test">
<img width="550px" src="https://raw.githubusercontent.com/zdharma/zinit/images/startup-times.png"/>
</a>
</p>

Zinit is an elastic and fast Zshell plugin manager that will allow you to
install everything from GitHub and other sites.

Zinit is currently the only plugin manager out there that has Turbo mode which yields
**50-80% faster Zsh startup!** (i.e.: the shell will start up to **5** times faster!). Check
out a speed comparison with other popular plugin managers
[here](https://github.com/zdharma/pm-perf-test).

Zinit gives **reports** from plugin load describing what aliases, functions,
bindkeys, Zle widgets, zstyles, completions, variables, `PATH` and `FPATH`
elements a plugin has set up.

Supported is **unloading** of plugin and ability to list, (un)install and
selectively disable, enable plugin's completions.

The system does not use `$FPATH`, loading multiple plugins doesn't clutter
`$FPATH` with the same number of entries (e.g. `10`). Code is immune to
`KSH_ARRAYS`. Completion management functionality is provided to allow user
to call `compinit` only once in `.zshrc`.

# Zinit Wiki

The information in this README is complemented by the [Zinit wiki](http://zdharma.org/zinit/wiki/). The README is an introductory overview of Zinit while the wiki gives a complete and in-depth information with examples. Make sure to read it to get the most out of Zinit.

# Installation

## Option 1 - Automatic Installation (Recommended)

The easiest way to install Zinit is to execute:

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zinit/master/doc/install.sh)"
```

This will install Zinit in `~/.zinit/bin`. `.zshrc` will be updated with three
lines of code that will be added to the bottom. The lines will be sourcing
`zinit.zsh` and setting up completion for command `zinit`. After installing and
reloading the shell compile Zinit with `zinit self-update`.

## Option 2 - Manual Installation

To manually install Zinit clone the repo to e.g. `~/.zinit/bin`:

```sh
mkdir ~/.zinit
git clone https://github.com/zdharma/zinit.git ~/.zinit/bin
```

and source it from `.zshrc` (above compinit):

```sh
source ~/.zinit/bin/zinit.zsh
```

If you place the `source` below `compinit`, then add those two lines after the `source`:
```sh
autoload -Uz _zinit
(( ${+_comps} )) && _comps[zinit]=_zinit
```

Various paths can be customized, see section [Customizing Paths](#customizing-paths).

After installing and reloading the shell compile Zinit with `zinit self-update`.

# Usage

## Introduction

[Click here to read the introduction to Zinit](http://zdharma.org/zinit/wiki/INTRODUCTION/). It explains basic usage and some of the more unique features of Zinit such as the Turbo mode. If you're new to Zinit we highly recommend you read it at least once.

## Example Usage

After installing Zinit you can start adding some actions (load some plugins) to `~/.zshrc`, at bottom. Some examples:

```zsh
# Two regular plugins loaded without tracking.
zinit light zsh-users/zsh-autosuggestions
zinit light zdharma/fast-syntax-highlighting

# Plugin history-search-multi-word loaded with tracking.
zinit load zdharma/history-search-multi-word

# Load the pure theme, with zsh-async library that's bundled with it.
zinit ice pick"async.zsh" src"pure.zsh"
zinit light sindresorhus/pure

# Binary release in archive, from GitHub-releases page.
# After automatic unpacking it provides program "fzf".
zinit ice from"gh-r" as"program"
zinit load junegunn/fzf-bin

# One other binary release, it needs renaming from `docker-compose-Linux-x86_64`.
# This is done by ice-mod `mv'{from} -> {to}'. There are multiple packages per
# single version, for OS X, Linux and Windows – so ice-mod `bpick' is used to
# select Linux package – in this case this is actually not needed, Zinit will
# grep operating system name and architecture automatically when there's no `bpick'.
zinit ice from"gh-r" as"program" mv"docker* -> docker-compose" bpick"*linux*"
zinit load docker/compose

# Vim repository on GitHub – a typical source code that needs compilation – Zinit
# can manage it for you if you like, run `./configure` and other `make`, etc. stuff.
# Ice-mod `pick` selects a binary program to add to $PATH. You could also install the
# package under the path $ZPFX, see: http://zdharma.org/zinit/wiki/Compiling-programs
zinit ice as"program" atclone"rm -f src/auto/config.cache; ./configure" \
    atpull"%atclone" make pick"src/vim"
zinit light vim/vim

# Scripts that are built at install (there's single default make target, "install",
# and it constructs scripts by `cat'ing a few files). The make'' ice could also be:
# `make"install PREFIX=$ZPFX"`, if "install" wouldn't be the only, default target.
zinit ice as"program" pick"$ZPFX/bin/git-*" make"PREFIX=$ZPFX"
zinit light tj/git-extras

# Handle completions without loading any plugin, see "clist" command.
# This one is to be ran just once, in interactive session.
zinit creinstall %HOME/my_completions
```

```zsh
# For GNU ls (the binaries can be gls, gdircolors, e.g. on OS X when installing the
# coreutils package from Homebrew; you can also use https://github.com/ogham/exa)
zinit ice atclone"dircolors -b LS_COLORS > c.zsh" atpull'%atclone' pick"c.zsh" nocompile'!'
zinit light trapd00r/LS_COLORS
```
[You can see an extended explanation of LS_COLORS in the wiki.](http://zdharma.org/zinit/wiki/LS_COLORS-explanation/)

```zsh
# make'!...' -> run make before atclone & atpull
zinit ice as"program" make'!' atclone'./direnv hook zsh > zhook.zsh' atpull'%atclone' src"zhook.zsh"
zinit light direnv/direnv
```
[You can see an extended explanation of direnv in the wiki.](http://zdharma.org/zinit/wiki/Direnv-explanation/)

If you're interested in more examples then check out the [zinit-configs
repository](https://github.com/zdharma/zinit-configs) where users have uploaded their
`~/.zshrc` and Zinit configurations. Feel free to
[submit](https://github.com/zdharma/zinit-configs/issues/new?template=request-to-add-zshrc-to-the-zinit-configs-repo.md)
your `~/.zshrc` there if it contains Zinit commands.

You can also check out the [Gallery of Zinit Invocations](http://zdharma.org/zinit/wiki/GALLERY/) for some additional examples.

## Ice Modifiers

Following `ice` modifiers are to be
[passed](http://zdharma.org/zinit/wiki/Alternate-Ice-Syntax/) to `zinit ice ...` to
obtain described effects.  The word `ice` means something that's added (like ice to a
drink) – and in Zinit it means adding modifier to a next `zinit` command, and also
something that's temporary because it melts – and this means that the modification will
last only for a **single** next `zinit` command.

Some Ice-modifiers are highlighted and clicking on them will take you to the
appropriate wiki page for an extended explanation.

You may safely assume a given ice works with both plugins and snippets unless
explicitly stated otherwise.

### Cloning Options
| Modifier | Description |
|:-:|-|
| `proto` |<div align="justify" style="text-align: justify;"> Change protocol to `git`,`ftp`,`ftps`,`ssh`, `rsync`, etc. Default is `https`. **Does not work with snippets.** </div>|
| [**`from`**](http://zdharma.org/zinit/wiki/Private-Repositories/) |<div align="justify" style="text-align: justify;"> Clone plugin from given site. Supported are `from"github"` (default), `..."github-rel"`, `..."gitlab"`, `..."bitbucket"`, `..."notabug"` (short names: `gh`, `gh-r`, `gl`, `bb`, `nb`). Can also be a full domain name (e.g. for GitHub enterprise). **Does not work with snippets.**</div>|
| `ver` |<div align="justify" style="text-align: justify;"> Used with `from"gh-r"` (i.e. downloading a binary release, e.g. for use with `as"program"`) – selects which version to download. Default is latest, can also be explicitly `ver"latest"`. Works also with regular plugins, checkouts e.g. `ver"abranch"`, i.e. a specific version. **Does not work with snippets.**</div>|
| `bpick` |<div align="justify" style="text-align: justify;"> Used to select which release from GitHub Releases to download, e.g. `zini ice from"gh-r" as"program" bpick"*Darwin*"; zini load docker/compose`. **Does not work with snippets.** </div>|
| `depth` |<div align="justify" style="text-align: justify;"> Pass `--depth` to `git`, i.e. limit how much of history to download. **Does not work with snippets.**</div>|
| `cloneopts` |<div align="justify" style="text-align: justify;"> Pass the contents of `cloneopts` to `git clone`. Defaults to `--recursive` i.e. Change cloning options. **Does not work with snippets.** </div>|
| `svn` |<div align="justify" style="text-align: justify;"> Use Subversion for downloading snippet. GitHub supports `SVN` protocol, this allows to clone subdirectories as snippets, e.g. `zinit ice svn; zinit snippet OMZ::plugins/git`. Other ice `pick` can be used to select file to source (default are: `*.plugin.zsh`, `init.zsh`, `*.zsh-theme`). **Does not work with plugins.**</div>|

### Selection of Files (To Source, …)
| Modifier | Description |
|:-:|-|
| [**`pick`**](http://zdharma.org/zinit/wiki/Sourcing-multiple-files/) |<div align="justify" style="text-align: justify;"> Select the file to source, or the file to set as command (when using `snippet --command` or the ice `as"program"`); it is a pattern, alphabetically first matched file is being chosen; e.g. `zinit ice pick"*.plugin.zsh"; zinit load …`.</div>|
| [**`src`**](http://zdharma.org/zinit/wiki/Sourcing-multiple-files) |<div align="justify" style="text-align: justify;"> Specify additional file to source after sourcing main file or after setting up command (via `as"program"`). It is not a pattern but a plain file name.</div>|
| [**`multisrc`**](http://zdharma.org/zinit/wiki/Sourcing-multiple-files) |<div align="justify" style="text-align: justify;"> Allows to specify multiple files for sourcing, enumerated with spaces as the separators (e.g. `multisrc'misc.zsh grep.zsh'`) and also using brace-expansion syntax (e.g. `multisrc'{misc,grep}.zsh'`). Supports patterns.</div>|

### Conditional Loading
| Modifier | Description |
|:-:|-|
| [**`wait`**](http://zdharma.org/zinit/wiki/Example-wait-conditions) |<div align="justify" style="text-align: justify;"> Postpone loading a plugin or snippet. For `wait'1'`, loading is done `1` second after prompt. For `wait'[[ ... ]]'`, `wait'(( ... ))'`, loading is done when given condition is meet. For `wait'!...'`, prompt is reset after load. Zsh can start 80% (i.e.: 5x) faster thanks to postponed loading. **Fact:** when `wait` is used without value, it works as `wait'0'`.</div>|
| [**`load`**](http://zdharma.org/zinit/wiki/Multiple-prompts) |<div align="justify" style="text-align: justify;"> A condition to check which should cause plugin to load. It will load once, the condition can be still true, but will not trigger second load (unless plugin is unloaded earlier, see `unload` below). E.g.: `load'[[ $PWD = */github* ]]'`.</div>|
| [**`unload`**](http://zdharma.org/zinit/wiki/Multiple-prompts) |<div align="justify" style="text-align: justify;"> A condition to check causing plugin to unload. It will unload once, then only if loaded again. E.g.: `unload'[[ $PWD != */github* ]]'`.</div>|
| `cloneonly` |<div align="justify" style="text-align: justify;"> Don't load the plugin / snippet, only download it </div>|
| `if` |<div align="justify" style="text-align: justify;"> Load plugin or snippet only when given condition is fulfilled, for example: `zinit ice if'[[ -n "$commands[otool]" ]]'; zinit load ...`.</div>|
| `has` |<div align="justify" style="text-align: justify;"> Load plugin or snippet only when given command is available (in $PATH), e.g. `zinit ice has'git' ...` </div>|
| `subscribe` / `on-update-of` |<div align="justify" style="text-align: justify;"> Postpone loading of a plugin or snippet until the given file(s) get updated, e.g. `subscribe'{~/files-*,/tmp/files-*}'` </div>|
| `trigger-load` |<div align="justify" style="text-align: justify;">Creates a function that loads the associated plugin/snippet, with an option (to use it, precede the ice content with `!`) to automatically forward the call afterwards, to a command of the same name as the function. Can obtain multiple functions to create – sparate with `;`.</div> |

### Plugin Output
| Modifier | Description |
|:-:|-|
| `silent` |<div align="justify" style="text-align: justify;"> Mute plugin's or snippet's `stderr` & `stdout`. Also skip `Loaded ...` message under prompt for `wait`, etc. loaded plugins, and completion-installation messages.</div>|
| `lucid` |<div align="justify" style="text-align: justify;"> Skip `Loaded ...` message under prompt for `wait`, etc. loaded plugins (a subset of `silent`).</div>|
| `notify` |<div align="justify" style="text-align: justify;"> Output given message under-prompt after successfully loading a plugin/snippet. In case of problems with the loading, output a warning message and the return code. If starts with `!` it will then always output the given message. Hint: if the message is empty, then it will just notify about problems.</div>|

### Completions
| Modifier | Description |
|:-:|-|
| `blockf` |<div align="justify" style="text-align: justify;"> Disallow plugin to modify `fpath`. Useful when a plugin wants to provide completions in traditional way. Zinit can manage completions and plugin can be blocked from exposing them.</div>|
| `nocompletions` |<div align="justify" style="text-align: justify;"> Don't detect, install and manage completions for this plugin. Completions can be installed later with `zinit creinstall {plugin-spec}`.</div>|

### Command Execution After Cloning, Updating or Loading
| Modifier | Description |
|:-:|-|
| `mv` |<div align="justify" style="text-align: justify;"> Move file after cloning or after update (then, only if new commits were downloaded). Example: `mv "fzf-* -> fzf"`. It uses `->` as separator for old and new file names. Works also with snippets.</div>|
| `cp` |<div align="justify" style="text-align: justify;"> Copy file after cloning or after update (then, only if new commits were downloaded). Example: `cp "docker-c* -> dcompose"`. Ran after `mv`.</div>|
| [**`atclone`**](http://zdharma.org/zinit/wiki/atload-and-other-at-ices) |<div align="justify" style="text-align: justify;"> Run command after cloning, within plugin's directory, e.g. `zinit ice atclone"echo Cloned"`. Ran also after downloading snippet.</div>|
| [**`atpull`**](http://zdharma.org/zinit/wiki/atload-and-other-at-ices) |<div align="justify" style="text-align: justify;"> Run command after updating (**only if new commits are waiting for download**), within plugin's directory. If starts with "!" then command will be ran before `mv` & `cp` ices and before `git pull` or `svn update`. Otherwise it is ran after them. Can be `atpull'%atclone'`, to repeat `atclone` Ice-mod.</div>|
| [**`atinit`**](http://zdharma.org/zinit/wiki/atload-and-other-at-ices) |<div align="justify" style="text-align: justify;"> Run command after directory setup (cloning, checking it, etc.) of plugin/snippet but before loading.</div>|
| [**`atload`**](http://zdharma.org/zinit/wiki/atload-and-other-at-ices) |<div align="justify" style="text-align: justify;"> Run command after loading, within plugin's directory. Can be also used with snippets. Passed code can be preceded with `!`, it will then be tracked (if using `load`, not `light`).</div>|
| `run-atpull` |<div align="justify" style="text-align: justify;"> Always run the atpull hook (when updating), not only when there are new commits to be downloaded.</div>|
| `nocd` |<div align="justify" style="text-align: justify;"> Don't switch the current directory into the plugin's directory when evaluating the above ice-mods `atinit''`,`atload''`, etc.</div>|
| [**`make`**](http://zdharma.org/zinit/wiki/Installing-with-make) |<div align="justify" style="text-align: justify;"> Run `make` command after cloning/updating and executing `mv`, `cp`, `atpull`, `atclone` Ice mods. Can obtain argument, e.g. `make"install PREFIX=/opt"`. If the value starts with `!` then `make` is ran before `atclone`/`atpull`, e.g. `make'!'`.</div>|
| `countdown` |<div align="justify" style="text-align: justify;">Causes an interruptable (by Ctrl-C) countdown 5…4…3…2…1…0 to be displayed before executing `atclone''`,`atpull''` and `make` ices</div>|
| `reset` |<div align="justify" style="text-align: justify;">Invokes `git reset --hard HEAD` for plugins or `svn revert` for SVN snippets before pulling any new changes. This way `git` or `svn` will not report conflicts if some changes were done in e.g.: `atclone''` ice. For file snippets and `gh-r` plugins it invokes `rm -rf *`.</div>|

### Sticky-Emulation Of Other Shells
| Modifier | Description |
|:-:|-|
| `sh`, `!sh` |<div align="justify" style="text-align: justify;">Source the plugin's (or snippet's) script with `sh` emulation so that also all functions declared within the file will get a *sticky* emulation assigned – when invoked they'll execute also with the `sh` emulation set-up. The `!sh` version switches additional options that are rather not important from the portability perspective.</div>|
| `bash`, `!bash` |<div align="justify" style="text-align: justify;">The same as `sh`, but with the `SH_GLOB` option disabled, so that Bash regular expressions work.</div>|
| `ksh`, `!ksh` |<div align="justify" style="text-align: justify;">The same as `sh`, but emulating `ksh` shell.</div>|
| `csh`, `!csh` |<div align="justify" style="text-align: justify;">The same as `sh`, but emulating `csh` shell.</div>|

### Others
| Modifier | Description |
|:-:|-|
| `as` |<div align="justify" style="text-align: justify;"> Can be `as"program"` (also the alias: `as"command"`), and will cause to add script/program to `$PATH` instead of sourcing (see `pick`). Can also be `as"completion"` – use with plugins or snippets in whose only underscore-starting `_*` files you are interested in. The third possible value is `as"null"` – a shorthand for `pick"/dev/null" nocompletions` – i.e.: it disables the default script-file sourcing and also the installation of completions.</div>|
| [**`id-as`**](http://zdharma.org/zinit/wiki/id-as/) |<div align="justify" style="text-align: justify;"> Nickname a plugin or snippet, to e.g. create a short handler for long-url snippet.</div>|
| `compile` |<div align="justify" style="text-align: justify;"> Pattern (+ possible `{...}` expansion, like `{a/*,b*}`) to select additional files to compile, e.g. `compile"(pure\|async).zsh"` for `sindresorhus/pure`.</div> |
| `nocompile` |<div align="justify" style="text-align: justify;"> Don't try to compile `pick`-pointed files. If passed the exclamation mark (i.e. `nocompile'!'`), then do compile, but after `make''` and `atclone''` (useful if Makefile installs some scripts, to point `pick''` at the location of their installation).</div>|
| `service` |<div align="justify" style="text-align: justify;"> Make following plugin or snippet a *service*, which will be ran in background, and only in single Zshell instance. See [zservices-organization](https://github.com/zservices) page.</div>|
| `reset-prompt` |<div align="justify" style="text-align: justify;"> Reset the prompt after loading the plugin/snippet (by issuing `zle .reset-prompt`). Note: normally it's sufficient to precede the value of `wait''` ice with `!`.</div>|
| `bindmap` |<div align="justify" style="text-align: justify;"> To hold `;`-separated strings like `Key(s)A -> Key(s)B`, e.g. `^R -> ^T; ^A -> ^B`. In general, `bindmap''`changes bindings (done with the `bindkey` builtin) the plugin does. The example would cause the plugin to map Ctrl-T instead of Ctrl-R, and Ctrl-B instead of Ctrl-A. **Does not work with snippets.**</div>|
| `trackbinds` |<div align="justify" style="text-align: justify;"> Shadow but only `bindkey` calls even with `zinit light ...`, i.e. even with tracking disabled (fast loading), to allow `bindmap` to remap the key-binds. The same effect has `zinit light -b ...`, i.e. additional `-b` option to the `light`-subcommand. **Does not work with snippets.**</div>|
| [**`wrap-track`**](http://zdharma.org/zinit/wiki/wrap-track) |<div align="justify" style='text-align: justify;'> Takes a `;`-separated list of function names that are to be tracked (meaning gathering report and unload data) **once** during execution. It works by wrapping the functions with a tracking-enabling and disabling snippet of code. In summary, `wrap-track` allows to extend the tracking beyond the moment of loading of a plugin. Example use is to `wrap-track` a precmd function of a prompt (like `_p9k_precmd()` of powerlevel10k) or other plugin that _postpones its initialization till the first prompt_ (like e.g.: zsh-autosuggestions). **Does not work with snippets.**</div>|
| `aliases` |<div align="justify" style="text-align: justify;">Load the plugin with the aliases mechanism enabled. Use with plugins that define **and use** aliases in their scripts.</div>|
| `light-mode` |<div align="justify" style="text-align: justify;">Load the plugin without the tracking, i.e.: as if it would be loaded with the `light` command. Useful for the for-syntax, where there is no `load` nor `light` subcommand</div>|

### Order of Execution

Order of execution of related Ice-mods: `atinit` -> `atpull!` -> `make'!!'` -> `mv` -> `cp` -> `make!` -> `atclone`/`atpull` -> `make` -> `(plugin script loading)` -> `src` -> `multisrc` -> `atload`.

## Zinit Commands

Following commands are passed to `zinit ...` to obtain described effects.

### Help

| Command | Description |
|:-:|-|
| `-h, --help, help` |<div align="justify" style="text-align: justify;"> Usage information.</div>|
| `man` |<div align="justify" style="text-align: justify;"> Manual.</div>|

### Loading and Unloading

| Command | Description |
|:-:|-|
| `load {plg-spec}` |<div align="justify" style="text-align: justify;"> Load plugin, can also receive absolute local path.</div>|
| `light [-b] {plg-spec}` |<div align="justify" style="text-align: justify;"> Light plugin load, without reporting/tracking. `-b` – track `bindkey`-calls only. There's also `light-mode` ice which can be used to induce the no-tracking (i.e.: *light*) loading, regardless of the command used.</div>|
| `unload [-q] {plg-spec}` |<div align="justify" style="text-align: justify;"> Unload plugin loaded with `zinit load ...`. `-q` – quiet.</div>|
| `snippet [-f] {url}` |<div align="justify" style="text-align: justify;"> Source local or remote file (by direct URL). `-f` – don't use cache (force redownload).</div>|

### Completions

| Command | Description |
|:-:|-|
| <code> clist [*columns*], completions [*columns*] </code> |<div align="justify" style="text-align: justify;"> List completions in use, with <code>*columns*</code> completions per line. `zpl clist 5` will for example print 5 completions per line. Default is 3.</div>|
| `cdisable {cname}` |<div align="justify" style="text-align: justify;"> Disable completion `cname`.</div>|
| `cenable {cname}` |<div align="justify" style="text-align: justify;"> Enable completion `cname`.</div>|
| `creinstall [-q] {plg-spec}` |<div align="justify" style="text-align: justify;"> Install completions for plugin, can also receive absolute local path. `-q` – quiet.</div>|
| `cuninstall {plg-spec}` |<div align="justify" style="text-align: justify;"> Uninstall completions for plugin.</div>|
| `csearch` |<div align="justify" style="text-align: justify;"> Search for available completions from any plugin.</div>|
| `compinit` |<div align="justify" style="text-align: justify;"> Refresh installed completions.</div>|
| `cclear` |<div align="justify" style="text-align: justify;"> Clear stray and improper completions.</div>|
| `cdlist` |<div align="justify" style="text-align: justify;"> Show compdef replay list.</div>|
| `cdreplay [-q]` |<div align="justify" style="text-align: justify;"> Replay compdefs (to be done after compinit). `-q` – quiet.</div>|
| `cdclear [-q]` |<div align="justify" style="text-align: justify;"> Clear compdef replay list. `-q` – quiet.</div>|

### Tracking of the Active Session

| Command | Description |
|:-:|-|
| `dtrace, dstart` |<div align="justify" style="text-align: justify;"> Start tracking what's going on in session.</div>|
| `dstop` |<div align="justify" style="text-align: justify;"> Stop tracking what's going on in session.</div>|
| `dunload` |<div align="justify" style="text-align: justify;"> Revert changes recorded between dstart and dstop.</div>|
| `dreport` |<div align="justify" style="text-align: justify;"> Report what was going on in session.</div>|
| `dclear` |<div align="justify" style="text-align: justify;"> Clear report of what was going on in session.</div>|

### Reports and Statistics

| Command | Description |
|:-:|-|
| `times [-s] [-m]` |<div align="justify" style="text-align: justify;"> Statistics on plugin load times, sorted in order of loading. `-s` – use seconds instead of milliseconds. `-m` – show plugin loading moments.</div>|
| `zstatus` |<div align="justify" style="text-align: justify;"> Overall Zinit status.</div>|
| `report {plg-spec}\|--all` |<div align="justify" style="text-align: justify;"> Show plugin report. `--all` – do it for all plugins.</div>|
| `loaded [keyword], list [keyword]` |<div align="justify" style="text-align: justify;"> Show what plugins are loaded (filter with 'keyword').</div>|
| `ls` |<div align="justify" style="text-align: justify;"> List snippets in formatted and colorized manner. Requires **tree** program.</div>|
| `status {plg-spec}\|URL\|--all` |<div align="justify" style="text-align: justify;"> Git status for plugin or svn status for snippet. `--all` – do it for all plugins and snippets.</div>|
| `recently [time-spec]` |<div align="justify" style="text-align: justify;"> Show plugins that changed recently, argument is e.g. 1 month 2 days.</div>|
| `bindkeys` |<div align="justify" style="text-align: justify;"> Lists bindkeys set up by each plugin.</div>|

### Compiling

| Command | Description |
|:-:|-|
| `compile {plg-spec}\|--all` |<div align="justify" style="text-align: justify;"> Compile plugin. `--all` – compile all plugins.</div>|
| `uncompile {plg-spec}\|--all` |<div align="justify" style="text-align: justify;"> Remove compiled version of plugin. `--all` – do it for all plugins.</div>|
| `compiled` |<div align="justify" style="text-align: justify;"> List plugins that are compiled.</div>|

### Other

| Command | Description |
|:-:|-|
| `self-update` |<div align="justify" style="text-align: justify;"> Updates and compiles Zinit.</div>|
| `update [-q] [-r] {plg-spec}\|URL\|--all` |<div align="justify" style="text-align: justify;"> Git update plugin or snippet.<br> `--all` – update all plugins and snippets.<br>  `-q` – quiet.<br> `-r` \| `--reset` – run `git reset --hard` / `svn revert` before pulling changes.</div>|
| `ice <ice specification>` |<div align="justify" style="text-align: justify;"> Add ice to next command, argument is e.g. from"gitlab".</div>|
| `delete {plg-spec}\|URL\|--clean\|--all` |<div align="justify" style="text-align: justify;"> Remove plugin or snippet from disk (good to forget wrongly passed ice-mods).  <br> `--all` – purge.<br> `--clean` – delete plugins and snippets that are not loaded.</div>|
| `cd {plg-spec}` |<div align="justify" style="text-align: justify;"> Cd into plugin's directory. Also support snippets if fed with URL.</div>|
| `edit {plg-spec}` |<div align="justify" style="text-align: justify;"> Edit plugin's file with $EDITOR.</div>|
| `glance {plg-spec}` |<div align="justify" style="text-align: justify;"> Look at plugin's source (pygmentize, {,source-}highlight).</div>|
| `stress {plg-spec}` |<div align="justify" style="text-align: justify;"> Test plugin for compatibility with set of options.</div>|
| `changes {plg-spec}` |<div align="justify" style="text-align: justify;"> View plugin's git log.</div>|
| `create {plg-spec}` |<div align="justify" style="text-align: justify;"> Create plugin (also together with GitHub repository).</div>|
| `srv {service-id} [cmd]` |<div align="justify" style="text-align: justify;"> Control a service, command can be: stop,start,restart,next,quit; `next` moves the service to another Zshell.</div>|
| `recall {plg-spec}\|URL` |<div align="justify" style="text-align: justify;"> Fetch saved ice modifiers and construct `zinit ice ...` command.</div>|
| `env-whitelist [-v] [-h] {env..}` |<div align="justify" style="text-align: justify;"> Allows to specify names (also patterns) of variables left unchanged during an unload. `-v` – verbose.</div>|
| `module` |<div align="justify" style="text-align: justify;"> Manage binary Zsh module shipped with Zinit, see `zinit module help`.</div>|
| `add-fpath\|fpath` `[-f\|--front]` `{plg-spec}` `[subdirectory]` |<div align="justify" style="text-align: justify;">Adds given plugin (not yet snippet) directory to `$fpath`. If the second argument is given, it is appended to the directory path. If the option `-f`/`--front` is given, the directory path is prepended instead of appended to `$fpath`. The `{plg-spec}` can be absolute path, i.e.: it's possible to also add regular directories.</div>|
| `run` `[-l]` `[plugin]` `{command}` |<div align="justify" style="text-align: justify;">Runs the given command in the given plugin's directory. If the option `-l` will be given then the plugin should be skipped – the option will cause the previous plugin to be reused.</div>|

## Updating Zinit and Plugins

To update Zinit issue `zinit self-update` in the command line.

To update all plugins and snippets, issue `zinit update`. If you wish to update only
a single plugin/snippet instead issue `zinit update NAME_OF_PLUGIN`. A list of
commits will be shown:

<p align="center">
<img src="./doc/img/update.png" />
</p>

Some plugins require performing an action each time they're updated. One way you can do
this is by using the `atpull` ice modifier. For example, writing `zinit ice atpull'./configure'` before loading a plugin will execute `./configure` after a successful update. Refer to [Ice Modifiers](#ice-modifiers) for more information.

The ice modifiers for any plugin or snippet are stored in their directory in a
`._zinit` subdirectory, hence the plugin doesn't have to be loaded to be correctly
updated. There's one other file created there, `.zinit_lstupd` – it holds the log of
the new commits pulled-in in the last update.

## Using Oh My Zsh Themes

To use **themes** created for Oh My Zsh you might want to first source the `git` library there:

```SystemVerilog
zinit snippet http://github.com/ohmyzsh/ohmyzsh/raw/master/lib/git.zsh
# Or using OMZ:: shorthand:
zinit snippet OMZ::lib/git.zsh
```

If the library will not be loaded, then similar to following errors will be appearing:

```
........:1: command not found: git_prompt_status
........:1: command not found: git_prompt_short_sha
```

Then you can use the themes as snippets (`zinit snippet {file path or GitHub URL}`).
Some themes require not only Oh My Zsh's Git **library**, but also Git **plugin** (error
about `current_branch` function can be appearing). Load this Git-plugin as single-file
snippet directly from OMZ:

```SystemVerilog
zinit snippet OMZ::plugins/git/git.plugin.zsh
```

Such lines should be added to `.zshrc`. Snippets are cached locally, use `-f` option to download
a fresh version of a snippet, or `zinit update {URL}`. Can also use `zinit update --all` to
update all snippets (and plugins).

Most themes require `promptsubst` option (`setopt promptsubst` in `zshrc`), if it isn't set, then
prompt will appear as something like: `... $(build_prompt) ...`.

You might want to suppress completions provided by the git plugin by issuing `zinit cdclear -q`
(`-q` is for quiet) – see below **Ignoring Compdefs**.

To summarize:

```SystemVerilog
# Load OMZ Git library
zinit snippet OMZ::lib/git.zsh

# Load Git plugin from OMZ
zinit snippet OMZ::plugins/git/git.plugin.zsh
zinit cdclear -q # <- forget completions provided up to this moment

setopt promptsubst

# Load theme from OMZ
zinit snippet OMZ::themes/dstufft.zsh-theme

# Load normal GitHub plugin with theme depending on OMZ Git library
zinit light NicoSantangelo/Alpharized
```

See also the Wiki page: [Example Oh My Zsh
Setup](http://zdharma.org/zinit/wiki/Example-Oh-My-Zsh-setup/).

# Completions

## Calling `compinit` Without Turbo Mode

With no Turbo mode in use, compinit can be called normally, i.e.: as `autoload compinit;
compinit`. This should be done after loading of all plugins and before possibly calling
`zinit cdreplay`. 

The `cdreplay` subcommand is provided to re-play all catched `compdef` calls. The
`compdef` calls are used to define a completion for a command. For example, `compdef
_git git` defines that the `git` command should be completed by a `_git` function.

The `compdef` function is provided by `compinit` call. As it should be called later,
after loading all of the plugins, Zinit provides its own `compdef` function that
catches (i.e.: records in an array) the arguments of the call, so that the loaded
plugins can freely call `compdef`. Then, the `cdreplay` (*compdef-replay*) can be used,
after `compinit` will be called (and the original `compdef` function will become
available), to execute all detected `compdef` calls. To summarize:

```sh
source ~/.zinit/bin/zinit.zsh

zinit load "some/plugin"
...
compdef _gnu_generic fd  # this will be intercepted by Zinit, because as the compinit
                         # isn't yet loaded, thus there's no such function `compdef'; yet
                         # Zinit provides its own `compdef' function which saves the
                         # completion-definition for later possible re-run with `zinit
                         # cdreplay' or `zicdreplay' (the second one can be used in hooks
                         # like atload'', atinit'', etc.)
...
zinit load "other/plugin"

autoload -Uz compinit
compinit

zinit cdreplay -q   # -q is for quiet; actually run all the `compdef's saved before
                    #`compinit` call (`compinit' declares the `compdef' function, so
                    # it cannot be used until `compinit' is ran; Zinit solves this
                    # via intercepting the `compdef'-calls and storing them for later
                    # use with `zinit cdreplay')
```

This allows to call compinit once.
Performance gains are huge, example shell startup time with double `compinit`: **0.980** sec, with
`cdreplay` and single `compinit`: **0.156** sec.

## Calling `compinit` With Turbo Mode

If you load completions using `wait''` Turbo mode then you can add
`atinit'zicompinit'` to syntax-highlighting plugin (which should be the last
one loaded, as their (2 projects, [z-sy-h](https://github.com/zsh-users/zsh-syntax-highlighting) &
[f-sy-h](https://github.com/zdharma/fast-syntax-highlighting))
 documentation state), or `atload'zicompinit'` to last
completion-related plugin. `zicompinit` is a function that just runs `autoload
compinit; compinit`, created for convenience. There's also `zicdreplay` which
will replay any caught compdefs so you can also do: `atinit'zicompinit;
zicdreplay'`, etc. Basically, the whole topic is the same as normal `compinit` call,
but it is done in `atinit` or `atload` hook of the last related plugin with use of the
helper functions (`zicompinit`,`zicdreplay` & `zicdclear` – see below for explanation
of the last one). To summarize:

```zsh
source ~/.zinit/bin/zinit.zsh

# Load using the for-syntax
zinit wait lucid for \
    "some/plugin"
zinit wait lucid for \
    "other/plugin"

zinit wait lucid atload"zicompinit; zicdreplay" blockf for \
    zsh-users/zsh-completions
```

## Ignoring Compdefs

If you want to ignore compdefs provided by some plugins or snippets, place their load commands
before commands loading other plugins or snippets, and issue `zinit cdclear` (or
`zicdclear`, designed to be used in hooks like `atload''`):

```SystemVerilog
source ~/.zinit/bin/zinit.zsh
zinit snippet OMZ::plugins/git/git.plugin.zsh
zinit cdclear -q # <- forget completions provided by Git plugin

zinit load "some/plugin"
...
zinit load "other/plugin"

autoload -Uz compinit
compinit
zinit cdreplay -q # <- execute compdefs provided by rest of plugins
zinit cdlist # look at gathered compdefs
```

The `cdreplay` is important if you use plugins like
`OMZ::plugins/kubectl/kubectl.plugin.zsh` or `asdf-vm/asdf`, because those plugins call
`compdef`.

## Disabling System-Wide `compinit` Call (Ubuntu)

On Ubuntu users might get surprised that e.g. their completions work while they didn't
call `compinit` in their `.zshrc`. That's because the function is being called in
`/etc/zshrc`. To disable this call – what is needed to avoid the slowdown and if user
loads any completion-equipped plugins, i.e. almost on 100% – add the following lines to
`~/.zshenv`:

```zsh
# Skip the not really helping Ubuntu global compinit
skip_global_compinit=1
```

# Zinit Module

## Motivation

The module is a binary Zsh module (think about `zmodload` Zsh command, it's that topic) which transparently and
automatically **compiles sourced scripts**. Many plugin managers do not offer compilation of plugins, the module is
a solution to this. Even if a plugin manager does compile plugin's main script (like Zinit does), the script can
source smaller helper scripts or dependency libraries (for example, the prompt `geometry-zsh/geometry` does that)
and there are very few solutions to that, which are demanding (e.g. specifying all helper files in plugin load
command and tracking updates to the plugin – in Zinit case: by using `compile` ice-mod).

  ![image](https://raw.githubusercontent.com/zdharma/zinit/images/mod-auto-compile.png)

## Installation

### Without Zinit

To install just the binary Zinit module **standalone** (Zinit is not needed, the module can be used with any
other plugin manager), execute:

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zinit/master/doc/mod-install.sh)"
```

This script will display what to add to `~/.zshrc` (2 lines) and show usage instructions.

### With Zinit

Zinit users can build the module by issuing following command instead of running above `mod-install.sh` script
(the script is for e.g. `zgen` users or users of any other plugin manager):

```zsh
zinit module build
```

This command will compile the module and display instructions on what to add to `~/.zshrc`.

## Measuring Time of `source`s

Besides the compilation-feature, the module also measures **duration** of each script sourcing. Issue `zpmod
source-study` after loading the module at top of `~/.zshrc` to see a list of all sourced files with the time the
sourcing took in milliseconds on the left. This feature allows to profile the shell startup. Also, no script can
pass-through that check and you will obtain a complete list of all loaded scripts, like if Zshell itself was
tracking this. The list can be surprising.

## Debugging

To enable debug messages from the module set:

```zsh
typeset -g ZPLG_MOD_DEBUG=1
```

# Hints and Tips

## Customizing Paths

Following variables can be set to custom values, before sourcing Zinit. The
previous global variables like `$ZPLG_HOME` have been removed to not pollute
the namespace – there's single `$ZINIT` hash instead of `8` string
variables. Please update your dotfiles.

```
declare -A ZINIT  # initial Zinit's hash definition, if configuring before loading Zinit, and then:
```
| Hash Field | Description |
-------------|--------------
| ZINIT[BIN_DIR]         | Where Zinit code resides, e.g.: "~/.zinit/bin"                      |
| ZINIT[HOME_DIR]        | Where Zinit should create all working directories, e.g.: "~/.zinit" |
| ZINIT[PLUGINS_DIR]     | Override single working directory – for plugins, e.g. "/opt/zsh/zinit/plugins" |
| ZINIT[COMPLETIONS_DIR] | As above, but for completion files, e.g. "/opt/zsh/zinit/root_completions"     |
| ZINIT[SNIPPETS_DIR]    | As above, but for snippets |
| ZINIT[ZCOMPDUMP_PATH]  | Path to `.zcompdump` file, with the file included (i.e. its name can be different) |
| ZINIT[COMPINIT_OPTS]   | Options for `compinit` call (i.e. done by `zicompinit`), use to pass -C to speed up loading |
| ZINIT[MUTE_WARNINGS]   | If set to `1`, then mutes some of the Zinit warnings, specifically the `plugin already registered` warning |

There is also `$ZPFX`, set by default to `~/.zinit/polaris` – a directory
where software with `Makefile`, etc. can be pointed to, by e.g. `atclone'./configure --prefix=$ZPFX'`.

## Non-GitHub (Local) Plugins

Use `create` subcommand with user name `_local` (the default) to create plugin's
skeleton in `$ZINIT[PLUGINS_DIR]`. It will be not connected with GitHub repository
(because of user name being `_local`). To enter the plugin's directory use `cd` command
with just plugin's name (without `_local`, it's optional).

If user name will not be `_local`, then Zinit will create repository also on GitHub
and setup correct repository origin.


## Extending Git

There are several projects that provide git extensions. Installing them with
Zinit has many benefits:

 - all files are under `$HOME` – no administrator rights needed,
 - declarative setup (like Chef or Puppet) – copying `.zshrc` to different account
   brings also git-related setup,
 - easy update by e.g. `zinit update --all`.

Below is a configuration that adds multiple git extensions, loaded in Turbo mode,
two seconds after prompt:

```zsh
zinit ice wait"2" lucid as"program" pick"bin/git-dsf"
zinit light zdharma/zsh-diff-so-fancy

zinit ice wait"2" lucid as"program" pick"git-now"
zinit light iwata/git-now

zinit ice wait"2" lucid as"program" pick"$ZPFX/bin/git-alias" make"PREFIX=$ZPFX" nocompile
zinit light tj/git-extras

zinit ice wait"2" lucid as"program" atclone'perl Makefile.PL PREFIX=$ZPFX' atpull'%atclone' \
            make'install' pick"$ZPFX/bin/git-cal"
zinit light k4rthik/git-cal
```

Target directory for installed files is `$ZPFX` (`~/.zinit/polaris` by default).

## Preinstalling Plugins

If you create a Docker image that uses Zinit, or want to install Turbo-loaded
plugins before the shell starts interactively, you can invoke the
zinit-scheduler function in such a way, that it:

 - installs plugins without waiting for the prompt (i.e. it's script friendly),
 - installs **all** plugins instantly, without respecting the `wait''` argument.

To accomplish this, use `burst` argument and call `@zinit-scheduler` function.
Example `Dockerfile` entry:

``` zsh
RUN zsh -i -c -- '@zinit-scheduler burst || true'
```

An example `Dockerfile` can be found
[**here**](https://github.com/robobenklein/configs/blob/master/Dockerfile).

# Getting Help and Community

Do you need help or wish to get in touch with other Zinit users?

- Visit our subreddit [r/zinit](https://www.reddit.com/r/zinit/).

- Chat with us in our IRC channel. Connect to [chat.freenode.net:6697](ircs://chat.freenode.net:6697/%23zinit) (SSL) or [chat.freenode.net:6667](irc://chat.freenode.net:6667/%23zinit) and join #zinit. Following is a quick access via Webchat [![IRC](https://kiwiirc.com/buttons/chat.freenode.net/zinit.png)](https://kiwiirc.com/client/chat.freenode.net:+6697/#zinit)

- Or via Gitter [![Join the chat at https://gitter.im/zdharma/zinit][gitter-badge]][gitter-link]

[status-badge]: https://travis-ci.org/zdharma/zinit.svg?branch=master
[status-link]: https://travis-ci.org/zdharma/zinit
[MIT-badge]: https://img.shields.io/badge/license-MIT-blue.svg
[MIT-link]: ./LICENSE
[ver-badge]: https://img.shields.io/github/tag/zdharma/zinit.svg
[ver-link]: https://github.com/zdharma/zinit/releases
[act-badge]: https://img.shields.io/github/commit-activity/y/zdharma/zinit.svg
[gitter-badge]: https://badges.gitter.im/zdharma/zinit.svg
[gitter-link]: https://gitter.im/zdharma/zinit?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge

<!-- vim:tw=87
-->

## Contributors

### Code Contributors

This project exists thanks to all the people who contribute. [[Contribute](CONTRIBUTING.md)].
<a href="https://github.com/zdharma/zplugin/graphs/contributors"><img src="https://opencollective.com/zplugin/contributors.svg?width=890&button=false" /></a>

### Financial Contributors

Become a financial contributor and help us sustain our community. [[Contribute](https://opencollective.com/zplugin/contribute)]

#### Individuals

<a href="https://opencollective.com/zplugin"><img src="https://opencollective.com/zplugin/individuals.svg?width=890"></a>

#### Organizations

Support this project with your organization. Your logo will show up here with a link to your website. [[Contribute](https://opencollective.com/zplugin/contribute)]

<a href="https://opencollective.com/zplugin/organization/0/website"><img src="https://opencollective.com/zplugin/organization/0/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/1/website"><img src="https://opencollective.com/zplugin/organization/1/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/2/website"><img src="https://opencollective.com/zplugin/organization/2/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/3/website"><img src="https://opencollective.com/zplugin/organization/3/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/4/website"><img src="https://opencollective.com/zplugin/organization/4/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/5/website"><img src="https://opencollective.com/zplugin/organization/5/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/6/website"><img src="https://opencollective.com/zplugin/organization/6/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/7/website"><img src="https://opencollective.com/zplugin/organization/7/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/8/website"><img src="https://opencollective.com/zplugin/organization/8/avatar.svg"></a>
<a href="https://opencollective.com/zplugin/organization/9/website"><img src="https://opencollective.com/zplugin/organization/9/avatar.svg"></a>
