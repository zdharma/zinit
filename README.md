[![paypal](https://img.shields.io/badge/-Donate-yellow.svg?longCache=true&style=for-the-badge)](https://www.paypal.me/ZdharmaInitiative)
[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=D54B3S7C6HGME)
[![patreon](https://img.shields.io/badge/-Patreon-orange.svg?longCache=true&style=for-the-badge)](https://www.patreon.com/psprint)
<br/>New: You can request a feature when donating, even fancy or advanced ones get implemented this way. [There are
reports](DONATIONS.md) about what is being done with the money received.

<p align="center">
<img src="https://raw.githubusercontent.com/zdharma/zplugin/master/doc/img/zplugin.png" />
</p>

[![Status][status-badge]][status-link] [![MIT License][MIT-badge]][MIT-link] [![][ver-badge]][ver-link] ![][act-badge] [![Chat at https://gitter.im/zplugin/Lobby][lobby-badge]][lobby-link]

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [News](#news)
- [Getting Help](#getting-help)
- [Zplugin](#zplugin)
- [Installation](#installation)
  - [Option 1 - Automatic Installation (Recommended)](#option-1---automatic-installation-recommended)
  - [Option 2 - Manual Installation](#option-2---manual-installation)
- [Usage](#usage)
  - [Introduction](#introduction)
  - [Example Usage](#example-usage)
  - [Ice Modifiers](#ice-modifiers)
  - [Zplugin Commands](#zplugin-commands)
  - [Updating Zplugin and Plugins](#updating-zplugin-and-plugins)
  - [Using Oh My Zsh Themes](#using-oh-my-zsh-themes)
- [Completions](#completions)
  - [Calling `compinit` Without Turbo Mode](#calling-compinit-without-turbo-mode)
  - [Calling `compinit` With Turbo Mode](#calling-compinit-with-turbo-mode)
  - [Ignoring Compdefs](#ignoring-compdefs)
  - [Disabling System-Wide `compinit` Call (Ubuntu)](#disabling-system-wide-compinit-call-ubuntu)
- [Zplugin Module](#zplugin-module)
  - [Motivation](#motivation)
  - [Installation](#installation-1)
  - [Measuring Time of `source`s](#measuring-time-of-sources)
  - [Debugging](#debugging)
- [Hints and Tips](#hints-and-tips)
  - [Customizing Paths](#customizing-paths)
  - [Non-Github (Local) Plugins](#non-github-local-plugins)
  - [Extending Git](#extending-git)
- [Additional Resources](#additional-resources)
- [IRC Channel](#irc-channel)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# News

<details>
  <summary>Here are the new features and updates added to zplugin in the last 90 days.</summary>

* 29-07-2019
  - `delete` now supports following options:
    * `--all` – deletes all plugins and snippets (a purge, similar to `rm -rf
      ${ZPLGM[PLUGINS_DIR]} ${ZPLGM[SNIPPETS_DIR]}`)
    * `--clean` – deletes only plugins and snippets that are **currently not loaded**
      in the current session.

* 09-07-2019
  - Zplugin can now have **its own plugins**, called **z-plugins**! Check out an
    example but fully functional z-plugin
    [zdharma/z-p-submods](https://github.com/zdharma/z-p-submods) and a document that
    explains on how to implement your own z-plugin
    ([here](../../wiki/Z-PLUGINS)).

* 08-07-2019
  - You can now do `zplugin ice wait ...` and it will work as `zplugin ice wait'0' ...`
    :) I.e. when there's no value to the `wait''` ice then a value of `0` is being
    substituted.

* 02-07-2019
  - [Cooperation of Fast-Syntax-Highlighting and
    Zplugin](https://asciinema.org/a/254630) – a new precise highlighting for
    Zplugin in F-Sy-H.

* 01-07-2019
  - `atclone''`, `atpull''` & `make''` get run in the same subshell, thus an e.g.
    export done in `atclone''` will be visible during the `make`.

* 26-06-2019
  - `notify''` contents gets evaluated, i.e. can contain active code like `$(tail -1
    /var/log/messages)`, etc.

* 23-06-2019
  - New ice mod `subscribe''`/`on-update-of''` which works like the
    `wait''` ice-mod, i.e. defers loading of a plugin, but it **looks at
    modification time of the given file(s)**, and when it changes, it then
    triggers loading of the plugin/snippet:

    ```zsh
    % zplugin ice on-update-of'{~/files-*,/tmp/files-*}' lucid \
        atload"echo I have been loaded" \
        notify"Yes that's true :)"
    % zplugin load zdharma/null
    % touch ~/files-1
    The plugin has been loaded
    %
    Yes that's true :)
    ```
    The plugin/snippet will be sourced as many times as the file gets updated.

* 22-06-2019
  - New ice mod `reset-prompt` that will issue `zle .reset-prompt` after loading the
    plugin or snippet, causing the prompt to be recomputed. Useful with themes &
    turbo-mode.

  - New ice-mod `notify''` which will cause to display an under-prompt notification
    when the plugin or snippet gets loaded. E.g.:

    ```zsh
    % zplugin ice wait"0" lucid notify"zdharma/null has been loaded"
    % zplugin light zdharma/null
    %
    zdharma/null has been loaded
    ```

    In case of problems with the loading a warning message will be output:

    ```
    % zplugin ice notify atload'return 7'
    % zplugin light zdharma/null
    %
    notify: Plugin not loaded / loaded with problem, the return code: 7
    ```

    Refer to [Ice Modifiers](#ice-modifiers) section for a complete description.

* 29-05-2019
  - Turbo-Mode, i.e. the `wait''` ice-mode now supports a suffix – the letter `a`, `b`
    or `c`. The meaning is illustrated by the following example:

    ```zsh
    zplugin ice wait"0b" as"command" pick"wd.sh" atinit"echo Firing 1" lucid
    zplugin light mfaerevaag/wd
    zplugin ice wait"0a" as"command" pick"wd.sh" atinit"echo Firing 2" lucid
    zplugin light mfaerevaag/wd

    # The output
    Firing 2
    Firing 1
    ```

    As it can be seen, the second plugin has been loaded first. That's because there
    are now three sub-slots (the `a`, `b` and `c`) in which the plugin/snippet loadings
    can be put into. Plugins from the same time-slot with suffix `a` will be loaded
    before plugins with suffix `b`, etc.

    In other words, instead of `wait'1'` you can enter `wait'1a'`,
    `wait'1b'` and `wait'1c'` – to this way **impose order** on the loadings
    **regardless of the order of `zplugin` commands**. 

</details>

To see the full history check [the changelog](CHANGELOG.md).

# Getting help

If you need help you can do the following:

- [Check if the information is in the Zplugin Wiki](http://zdharma.org/zplugin/site/)

- Ask in our subreddit [r/zplugin](https://www.reddit.com/r/zplugin/).

- [Ask in our IRC channel.](#irc-channel) 

# Zplugin

Zplugin is an elastic and fast Zshell plugin manager that will allow you to
install everything from Github and other sites. 

Zplugin is currently the only plugin manager out there that has Turbo
Mode which yields **39-50%
faster Zsh startup!**

Zplugin gives **reports** from plugin load describing what aliases, functions,
bindkeys, Zle widgets, zstyles, completions, variables, `PATH` and `FPATH`
elements a plugin has set up.

Supported is **unloading** of plugin and ability to list, (un)install and
selectively disable, enable plugin's completions.

The system does not use `$FPATH`, loading multiple plugins doesn't clutter
`$FPATH` with the same number of entries (e.g. `10`). Code is immune to
`KSH_ARRAYS`. Completion management functionality is provided to allow user
to call `compinit` only once in `.zshrc`.

# Installation

## Option 1 - Automatic Installation (Recommended)

The easiest way to install Zplugin is to execute: 

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zplugin/master/doc/install.sh)"
```

This will install Zplugin in `~/.zplugin/bin`. `.zshrc` will be updated with three
lines of code that will be added to the bottom. The lines will be sourcing
`zplugin.zsh` and setting up completion for command `zplugin`. After installing and
reloading the shell compile Zplugin with `zplugin self-update`.

## Option 2 - Manual Installation

To manually install Zplugin clone the repo to e.g. `~/.zplugin/bin`:

```sh
mkdir ~/.zplugin
git clone https://github.com/zdharma/zplugin.git ~/.zplugin/bin
```

and source it from `.zshrc` (above compinit):

```sh
source ~/.zplugin/bin/zplugin.zsh
```

If you place the `source` below `compinit`, then add those two lines after the `source`:
```sh
autoload -Uz _zplugin
(( ${+_comps} )) && _comps[zplugin]=_zplugin
```

Various paths can be customized, see section [Customizing Paths](#customizing-paths).

After installing and reloading shell give Zplugin a quick try with `zplugin help` and
compile it with `zplugin self-update`.

# Usage

## Introduction

[Click here to read the introduction to Zplugin.](http://zdharma.org/zplugin/site/INTRODUCTION/). It explains basic usage and some of the more unique features of Zplugin such as the Turbo Mode. If you're new to Zplugin we highly recommend you read it at least once.

## Example Usage

After installing Zplugin you can start adding some actions (load some plugins) to `~/.zshrc`, at bottom. Some examples:

```zsh
# Two regular plugins loaded without tracking.
zplugin light zsh-users/zsh-autosuggestions
zplugin light zdharma/fast-syntax-highlighting

# Plugin history-search-multi-word loaded with tracking.
zplugin load zdharma/history-search-multi-word

# Binary release in archive, from Github-releases page. 
# After automatic unpacking it provides program "fzf".
zplugin ice from"gh-r" as"program"; zplugin load junegunn/fzf-bin

# One other binary release, it needs renaming from `docker-compose-Linux-x86_64`.
# This is done by ice-mod `mv'{from} -> {to}'. There are multiple packages per
# single version, for OS X, Linux and Windows – so ice-mod `bpick' is used to
# select Linux package – in this case this is actually not needed, Zplugin will
# grep operating system name and architecture automatically when there's no `bpick'.
zplugin ice from"gh-r" as"program" mv"docker* -> docker-compose" bpick"*linux*"
zplugin load docker/compose

# Vim repository on Github – a typical source code that needs compilation – Zplugin
# can manage it for you if you like, run `./configure` and other `make`, etc. stuff.
# Ice-mod `pick` selects a binary program to add to $PATH. You could also install the
# package under the path $ZPFX with make"install PREFIX=$ZPFX"
zplugin ice as"program" atclone"rm -f src/auto/config.cache; ./configure" \
    atpull"%atclone" make pick"src/vim"
zplugin light vim/vim

# Scripts that are built at install (there's single default make target, "install",
# and it constructs scripts by `cat'ing a few files). The make"" ice could also be:
# `make"install PREFIX=$ZPFX"`, if "install" wouldn't be the only, default target.
zplugin ice as"program" pick"$ZPFX/bin/git-*" make"PREFIX=$ZPFX"
zplugin light tj/git-extras

# Load the pure theme, with zsh-async library that's bundled with it.
zplugin ice pick"async.zsh" src"pure.zsh"; zplugin light sindresorhus/pure

# Handle completions without loading any plugin, see "clist" command.
# This one is to be ran just once, in interactive session.
zplugin creinstall %HOME/my_completions  
```

```zsh
# For GNU ls (the binaries can be gls, gdircolors, e.g. on OS X when installing the
# coreutils package from Homebrew or using https://github.com/ogham/exa)
zplugin ice atclone"dircolors -b LS_COLORS > c.zsh" atpull'%atclone' pick"c.zsh" nocompile'!'
zplugin light trapd00r/LS_COLORS
```
[You can see an extended explanation of LS_COLORS in the wiki.](http://zdharma.org/zplugin/site/LS_COLORS-explanation/)

```zsh
# make'!...' -> run make before atclone & atpull
zplugin ice as"program" make'!' atclone'./direnv hook zsh > zhook.zsh' atpull'%atclone' src"zhook.zsh"
zplugin light direnv/direnv
```
[You can see an extended explanation of direnv in the wiki.](http://zdharma.org/zplugin/site/Direnv-explanation/)

If you're interested in more examples then check out the [zplugin-configs 
repository](https://github.com/zdharma/zplugin-configs) where users have uploaded their
`~/.zshrc` and Zplugin configurations. Feel free to
[submit](https://github.com/zdharma/zplugin-configs/issues/new?template=request-to-add-zshrc-to-the-zplugin-configs-repo.md)
your `~/.zshrc` there if it contains Zplugin commands.

You can also check out the [Gallery of Zplugin Invocations](http://zdharma.org/zplugin/site/GALLERY/) for some additional examples.

## Ice Modifiers

Following `ice` modifiers are to be passed to `zplugin ice ...` to obtain described effects.
The word `ice` means something that's added (like ice to a drink) – and in Zplugin it
means adding modifier to a next `zplugin` command, and also something that's temporary
because it melts – and this means that the modification will last only for a **single**
next `zplugin` command.

Some Ice-modifiers are highlighted and clicking on them will take you to the
appropriate wiki page for an extended explanation.

| Modifier | <p align="center"> <p align="center"> Description </p> </p> | Works with plugins | Works with snippets |
|:-:|-|:-:|:-:|
| `proto` | Change protocol to `git`,`ftp`,`ftps`,`ssh`, `rsync`, etc. Default is `https`. | :heavy_check_mark: | :heavy_multiplication_x: |
| `from` | Clone plugin from given site. Supported are `from"github"` (default), `..."github-rel"`, `..."gitlab"`, `..."bitbucket"`, `..."notabug"` (short names: `gh`, `gh-r`, `gl`, `bb`, `nb`). Can also be a full domain name (e.g. for Github enterprise). | :heavy_check_mark: | :heavy_multiplication_x: |
| `as` | Can be `as"program"` (also alias `as"command"`), and will cause to add script/program to `$PATH` instead of sourcing (see `pick`). Can also be `as"completion"`. | :heavy_check_mark: | :heavy_check_mark: |
| [**`id-as`**](http://zdharma.org/zplugin/site/id-as/) | Nickname a plugin or snippet, to e.g. create a short handler for long-url snippet. | :heavy_check_mark: | :heavy_check_mark: |
| `ver` | Used with `from"gh-r"` (i.e. downloading a binary release, e.g. for use with `as"program"`) – selects which version to download. Default is latest, can also be explicitly `ver"latest"`. Works also with regular plugins, checkouts e.g. `ver"abranch"`, i.e. a specific version. | :heavy_check_mark: | :heavy_check_mark: |
| [**`pick`**](http://zdharma.org/zplugin/site/Sourcing-multiple-files/) | Select the file to source, or the file to set as command (when using `snippet --command` or ICE `as"program"`), e.g. `zplugin ice pick"*.plugin.zsh"`. | :heavy_check_mark: | :heavy_check_mark: |
| `bpick` | Used to select which release from Github Releases to download, e.g. `zplg ice from"gh-r" as"program" bpick"*Darwin*"; zplg load docker/compose` | :heavy_check_mark: | :heavy_multiplication_x: |
| `depth` | Pass `--depth` to `git`, i.e. limit how much of history to download. | :heavy_check_mark: | :heavy_multiplication_x: |
| `cloneopts` | Pass the contents of `cloneopts` to `git clone`. Defaults to `--recursive` i.e. Change cloning options. | :heavy_check_mark: | :heavy_multiplication_x: |
| `bindmap` | To hold `;`-separated strings like `Key(s)A -> Key(s)B`, e.g. `^R -> ^T; ^A -> ^B`. In general, `bindmap''`changes bindings (done with the `bindkey` builtin) the plugin does. The example would cause the plugin to map Ctrl-T instead of Ctrl-R, and Ctrl-B instead of Ctrl-A. | :heavy_check_mark: | :heavy_multiplication_x: |
| `trackbinds` | Shadow but only `bindkey` calls even with `zplugin light ...`, i.e. even with tracking disabled (fast loading), to allow `bindmap` to remap the key-binds. The same effect has `zplugin light -b ...`, i.e. additional `-b` option to the `light`-subcommand. | :heavy_check_mark: | :heavy_multiplication_x: |
| `if` | Load plugin or snippet only when given condition is fulfilled, for example: `zplugin ice if'[[ -n "$commands[otool]" ]]'; zplugin load ...`. | :heavy_check_mark: | :heavy_check_mark: |
| `has` | Load plugin or snippet only when given command is available (in $PATH), e.g. `zplugin ice has'git' ...` | :heavy_check_mark: | :heavy_check_mark: |
| `blockf` | Disallow plugin to modify `fpath`. Useful when a plugin wants to provide completions in traditional way. Zplugin can manage completions and plugin can be blocked from exposing them. | :heavy_check_mark: | :heavy_check_mark: |
| `silent` | Mute plugin's or snippet's `stderr` & `stdout`. Also skip `Loaded ...` message under prompt for `wait`, etc. loaded plugins, and completion-installation messages. | :heavy_check_mark: | :heavy_check_mark: |
| `lucid` | Skip `Loaded ...` message under prompt for `wait`, etc. loaded plugins (a subset of `silent`). | :heavy_check_mark: | :heavy_check_mark: |
| `notify` | Output given message under-prompt after successfully loading a plugin/snippet. In case of problems with the loading, output a warning message and the return code. If starts with `!` it will then always output the given message. Hint: if the message is empty, then it will just notify about problems. | :heavy_check_mark: | :heavy_check_mark: |
| `reset-prompt` | Reset the prompt after loading the plugin/snippet (by issuing `zle .reset-prompt`). Note: normally it's sufficient to precede the value of `wait''` ice with `!`. | :heavy_check_mark: | :heavy_check_mark: |
| `mv` | Move file after cloning or after update (then, only if new commits were downloaded). Example: `mv "fzf-* -> fzf"`. It uses `->` as separator for old and new file names. Works also with snippets. | :heavy_check_mark: | :heavy_check_mark: |
| `cp` | Copy file after cloning or after update (then, only if new commits were downloaded). Example: `cp "docker-c* -> dcompose"`. Ran after `mv`. | :heavy_check_mark: | :heavy_check_mark: |
| `atinit` | Run command after directory setup (cloning, checking it, etc.) of plugin/snippet but before loading. | :heavy_check_mark: | :heavy_check_mark: |
| `atclone` | Run command after cloning, within plugin's directory, e.g. `zplugin ice atclone"echo Cloned"`. Ran also after downloading snippet. | :heavy_check_mark: | :heavy_check_mark: |
| `atload` | Run command after loading, within plugin's directory. Can be also used with snippets. Passed code can be preceded with `!`, it will then be tracked (if using `load`, not `light`). | :heavy_check_mark: | :heavy_check_mark: |
| `atpull` | Run command after updating (**only if new commits are waiting for download**), within plugin's directory. If starts with "!" then command will be ran before `mv` & `cp` ices and before `git pull` or `svn update`. Otherwise it is ran after them. Can be `atpull'%atclone'`, to repeat `atclone` Ice-mod. | :heavy_check_mark: | :heavy_check_mark: |
| `run-atpull` | Always run the atpull hook (when updating), not only when there are new commits to be downloaded. | :heavy_check_mark: | :heavy_check_mark: |
| `cloneonly` | Don't load the plugin / snippet, only download it | :heavy_check_mark: | :heavy_check_mark: |
| `nocd` | Don't switch the current directory into the plugin's directory when evaluating the above ice-mods `atinit''`,`atload''`, etc. | :heavy_check_mark: | :heavy_check_mark: |
| `svn` | Use Subversion for downloading snippet. Github supports `SVN` protocol, this allows to clone subdirectories as snippets, e.g. `zplugin ice svn; zplugin snippet OMZ::plugins/git`. Other ice `pick` can be used to select file to source (default are: `*.plugin.zsh`, `init.zsh`, `*.zsh-theme`). | :heavy_multiplication_x: | :heavy_check_mark: |
| [**`make`**](http://zdharma.org/zplugin/site/Installing-with-make) | Run `make` command after cloning/updating and executing `mv`, `cp`, `atpull`, `atclone` Ice mods. Can obtain argument, e.g. `make"install PREFIX=/opt"`. If the value starts with `!` then `make` is ran before `atclone`/`atpull`, e.g. `make'!'`. | :heavy_check_mark: | :heavy_check_mark: |
| [**`src`**](http://zdharma.org/zplugin/site/Sourcing-multiple-files) | Specify additional file to source after sourcing main file or after setting up command (via `as"program"`). | :heavy_check_mark: | :heavy_check_mark: |
| [**`wait`**](http://zdharma.org/zplugin/site/Example-wait-conditions) | Postpone loading a plugin or snippet. For `wait'1'`, loading is done `1` second after prompt. For `wait'[[ ... ]]'`, `wait'(( ... ))'`, loading is done when given condition is meet. For `wait'!...'`, prompt is reset after load. Zsh can start 39% faster thanks to postponed loading (result obtained in test with `11` plugins). **Fact:** when `wait` is used without value, it works as `wait'0'`. | :heavy_check_mark: | :heavy_check_mark: |
| [**`load`**](http://zdharma.org/zplugin/site/Multiple-prompts) | A condition to check which should cause plugin to load. It will load once, the condition can be still true, but will not trigger second load (unless plugin is unloaded earlier, see `unload` below). E.g.: `load'[[ $PWD = */github* ]]'`. | :heavy_check_mark: | :heavy_check_mark: |
| [**`unload`**](http://zdharma.org/zplugin/site/Multiple-prompts) | A condition to check causing plugin to unload. It will unload once, then only if loaded again. E.g.: `unload'[[ $PWD != */github* ]]'`. | :heavy_check_mark: | :heavy_check_mark: |
| `subscribe` / `on-update-of` | Postpone loading of a plugin or snippet until the given file(s) get updated, e.g. `subscribe'{~/files-*,/tmp/files-*}'` | :heavy_check_mark: | :heavy_check_mark: |
| `service` | Make following plugin or snippet a *service*, which will be ran in background, and only in single Zshell instance. See [zservices org](https://github.com/zservices). | :heavy_check_mark: | :heavy_check_mark: |
| `compile` | Pattern (+ possible `{...}` expansion, like `{a/*,b*}`) to select additional files to compile, e.g. `compile"(pure\ | :heavy_check_mark: | :heavy_check_mark: |
| `nocompletions` | Don't detect, install and manage completions for this plugin. Completions can be installed later with `zplugin creinstall {plugin-spec}`. | :heavy_check_mark: | :heavy_check_mark: |
| `nocompile` | Don't try to compile `pick`-pointed files. If passed the exclamation mark (i.e. `nocompile'!'`), then do compile, but after `make''` and `atclone''` (useful if Makefile installs some scripts, to point `pick''` at location of installation). | :heavy_check_mark: | :heavy_check_mark: |
| [**`multisrc`**](http://zdharma.org/zplugin/site/Sourcing-multiple-files) | Allows to specify multiple files for sourcing, enumerated with spaces as the separator (e.g. `multisrc'misc.zsh grep.zsh'`) and also using brace-expansion syntax (e.g. `multisrc'{misc,grep}.zsh'`). | :heavy_check_mark: | :heavy_check_mark: |

Order of execution of related Ice-mods: `atinit` -> `atpull!` -> `make'!!'` -> `mv` -> `cp` -> `make!` -> `atclone`/`atpull` -> `make` -> `(plugin script loading)` -> `src` -> `multisrc` -> `atload`.

## Zplugin Commands

```
% zpl help
Usage:
—— -h|--help|help                – usage information
—— man                           – manual
—— self-update                   – updates and compiles Zplugin
—— times [-s]                    – statistics on plugin load times, sorted in order of loading; -s – use seconds instead of milliseconds
—— zstatus                       – overall Zplugin status
—— load plg-spec                 – load plugin, can also receive absolute local path
—— light [-b] plg-spec           – light plugin load, without reporting/tracking (-b – do track but bindkey-calls only)
—— unload plg-spec               – unload plugin loaded with `zplugin load ...', -q – quiet
—— snippet [-f] {url}            – source local or remote file (by direct URL), -f: force – don't use cache
—— ls                            – list snippets in formatted and colorized manner
—— ice <ice specification>       – add ICE to next command, argument is e.g. from"gitlab"
—— update [-q] plg-spec|URL      – Git update plugin or snippet (or all plugins and snippets if ——all passed); besides -q accepts also ——quiet, and also -r/--reset – this option causes to run git reset --hard / svn revert before pulling changes
—— status plg-spec|URL           – Git status for plugin or svn status for snippet (or for all those if ——all passed)
—— report plg-spec               – show plugin's report (or all plugins' if ——all passed)
—— delete [--clean|--all] plg-spec|URL – remove plugin or snippet from disk (good to forget wrongly passed ice-mods); --all – purge, --clean – delete plugins and snippets that are not loaded
—— loaded|list [keyword]         – show what plugins are loaded (filter with 'keyword')
—— cd plg-spec                   – cd into plugin's directory; also support snippets, if feed with URL
—— create plg-spec               – create plugin (also together with Github repository)
—— edit plg-spec                 – edit plugin's file with $EDITOR
—— glance plg-spec               – look at plugin's source (pygmentize, {,source-}highlight)
—— stress plg-spec               – test plugin for compatibility with set of options
—— changes plg-spec              – view plugin's git log
—— recently [time-spec]          – show plugins that changed recently, argument is e.g. 1 month 2 days
—— clist|completions             – list completions in use
—— cdisable cname                – disable completion `cname'
—— cenable cname                 – enable completion `cname'
—— creinstall plg-spec           – install completions for plugin, can also receive absolute local path; -q – quiet
—— cuninstall plg-spec           – uninstall completions for plugin
—— csearch                       – search for available completions from any plugin
—— compinit                      – refresh installed completions
—— dtrace|dstart                 – start tracking what's going on in session
—— dstop                         – stop tracking what's going on in session
—— dunload                       – revert changes recorded between dstart and dstop
—— dreport                       – report what was going on in session
—— dclear                        – clear report of what was going on in session
—— compile plg-spec              – compile plugin (or all plugins if ——all passed)
—— uncompile plg-spec            – remove compiled version of plugin (or of all plugins if ——all passed)
—— compiled                      – list plugins that are compiled
—— cdlist                        – show compdef replay list
—— cdreplay [-q]                 – replay compdefs (to be done after compinit), -q – quiet
—— cdclear [-q]                  – clear compdef replay list, -q – quiet
—— srv {service-id} [cmd]        – control a service, command can be: stop,start,restart,next,quit; `next' moves the service to another Zshell
—— recall plg-spec|URL      – fetch saved ice modifiers and construct `zplugin ice ...' command
—— env-whitelist [-v|-h] {env..} – allows to specify names (also patterns) of variables left unchanged during an unload. -v – verbose
—— bindkeys                      – lists bindkeys set up by each plugin
—— module                        – manage binary Zsh module shipped with Zplugin, see `zplugin module help'

Available ice-modifiers:
        svn proto from teleid bindmap cloneopts id-as depth if wait load
        unload blockf on-update-of subscribe pick bpick src as ver silent
        lucid notify mv cp atinit atclone atload atpull nocd run-atpull has
        cloneonly make service trackbinds multisrc compile nocompile
        nocompletions reset-prompt
```

## Updating Zplugin and Plugins

To update Zplugin issue `zplugin self-update` in the command line.

To update all plugins and snippets, issue `zplugin update`. If you wish to update only
a single plugin/snippet instead issue `zplugin update NAME_OF_PLUGIN`. A list of
commits will be shown:

<p align="center">
<img src="./doc/img/update.png" />
</p>

Some plugins require performing an action each time they're updated. One way you can do
this is by using the `atpull` ice modifier. For example, writing `zplugin ice atpull'./configure'` before loading a plugin will execute `./configure` after a successful update. Refer to [Ice Modifiers](#ice-modifiers) for more information.

## Using Oh My Zsh Themes

To use **themes** created for Oh My Zsh you might want to first source the `git` library there:

```SystemVerilog
zplugin snippet http://github.com/robbyrussell/oh-my-zsh/raw/master/lib/git.zsh
# Or using OMZ:: shorthand:
zplugin snippet OMZ::lib/git.zsh
```

If the library will not be loaded, then similar to following errors will be appearing:

```
........:1: command not found: git_prompt_status
........:1: command not found: git_prompt_short_sha
```

Then you can use the themes as snippets (`zplugin snippet {file path or Github URL}`).
Some themes require not only Oh My Zsh's Git **library**, but also Git **plugin** (error
about `current_branch` function can be appearing). Load this Git-plugin as single-file
snippet directly from OMZ:

```SystemVerilog
zplugin snippet OMZ::plugins/git/git.plugin.zsh
```

Such lines should be added to `.zshrc`. Snippets are cached locally, use `-f` option to download
a fresh version of a snippet, or `zplugin update {URL}`. Can also use `zplugin update --all` to
update all snippets (and plugins).

Most themes require `promptsubst` option (`setopt promptsubst` in `zshrc`), if it isn't set, then
prompt will appear as something like: `... $(build_prompt) ...`.

You might want to supress completions provided by the git plugin by issuing `zplugin cdclear -q`
(`-q` is for quiet) – see below **Ignoring Compdefs**.

To summarize:

```SystemVerilog
# Load OMZ Git library
zplugin snippet OMZ::lib/git.zsh

# Load Git plugin from OMZ
zplugin snippet OMZ::plugins/git/git.plugin.zsh
zplugin cdclear -q # <- forget completions provided up to this moment

setopt promptsubst

# Load theme from OMZ
zplugin snippet OMZ::themes/dstufft.zsh-theme

# Load normal Github plugin with theme depending on OMZ Git library
zplugin light NicoSantangelo/Alpharized
```

# Completions

## Calling `compinit` Without Turbo Mode

With no turbo mode in use, compinit can be called normally, i.e.: as `autoload compinit;
compinit`. This should be done after loading of all plugins and before possibly calling
`zplugin cdreplay`.  Also, plugins aren't allowed to simply run `compdefs`. You can
decide whether to run `compdefs` by issuing `zplugin cdreplay` (reads: `compdef`-replay).
To summarize:

```sh
source ~/.zplugin/bin/zplugin.zsh

zplugin load "some/plugin"
...
compdef _gnu_generic fd  # this will be intercepted by Zplugin, because as the compinit
                         # isn't yet loaded, thus there's no such function `compdef'; yet
                         # Zplugin provides its own `compdef' function which saves the
                         # completion-definition for later possible re-run with `zplugin
                         # cdreplay` or `zpcdreplay` (the second one can be used in hooks
                         # like atload'', atinit'', etc.)
...
zplugin load "other/plugin"

autoload -Uz compinit
compinit

zplugin cdreplay -q # -q is for quiet; actually run all the `compdef's saved before
                    #`compinit` call (`compinit' declares the `compdef' function, so
                    # it cannot be used until `compinit` is ran; Zplugin solves this
                    # via intercepting the `compdef'-calls and storing them for later
                    # use with `zplugin cdreplay')
```

This allows to call compinit once.
Performance gains are huge, example shell startup time with double `compinit`: **0.980** sec, with
`cdreplay` and single `compinit`: **0.156** sec.

## Calling `compinit` With Turbo Mode

If you load completions using `wait''` turbo-mode then you can add
`atinit'zpcompinit'` to syntax-highlighting plugin (which should be the last
one loaded, as their (2 projects, [z-sy-h](https://github.com/zsh-users/zsh-syntax-highlighting) &
[f-sy-h](https://github.com/zdharma/fast-syntax-highlighting))
 documentation state), or `atload'zpcompinit'` to last
completion-related plugin. `zpcompinit` is a function that just runs `autoload
compinit; compinit`, created for convenience. There's also `zpcdreplay` which
will replay any caught compdefs so you can also do: `atinit'zpcompinit;
zpcdreplay'`, etc. Basically, the whole topic is the same as normal `compinit` call,
but it is done in `atinit` or `atload` hook of the last related plugin with use of the
helper functions (`zpcompinit`,`zpcdreplay` & `zpcdclear` – see below for explanation
of the last one).

## Ignoring Compdefs

If you want to ignore compdefs provided by some plugins or snippets, place their load commands
before commands loading other plugins or snippets, and issue `zplugin cdclear` (or
`zpcdclear`, designed to be used in hooks like `atload''`):

```SystemVerilog
source ~/.zplugin/bin/zplugin.zsh
zplugin snippet OMZ::plugins/git/git.plugin.zsh
zplugin cdclear -q # <- forget completions provided by Git plugin

zplugin load "some/plugin"
...
zplugin load "other/plugin"

autoload -Uz compinit
compinit
zplugin cdreplay -q # <- execute compdefs provided by rest of plugins
zplugin cdlist # look at gathered compdefs
```

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

# Zplugin Module

## Motivation

The module is a binary Zsh module (think about `zmodload` Zsh command, it's that topic) which transparently and
automatically **compiles sourced scripts**. Many plugin managers do not offer compilation of plugins, the module is
a solution to this. Even if a plugin manager does compile plugin's main script (like Zplugin does), the script can
source smaller helper scripts or dependency libraries (for example, the prompt `geometry-zsh/geometry` does that)
and there are very few solutions to that, which are demanding (e.g. specifying all helper files in plugin load
command and tracking updates to the plugin – in Zplugin case: by using `compile` ice-mod).

  ![image](https://raw.githubusercontent.com/zdharma/zplugin/images/mod-auto-compile.png)

## Installation

**Without Zplugin**

To install just the binary Zplugin module **standalone** (Zplugin is not needed, the module can be used with any
other plugin manager), execute:

```zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/zdharma/zplugin/master/doc/mod-install.sh)"
```

This script will display what to add to `~/.zshrc` (2 lines) and show usage instructions.

**With Zplugin**

Zplugin users can build the module by issuing following command instead of running above `mod-install.sh` script
(the script is for e.g. `zgen` users or users of any other plugin manager):

```zsh
zplugin module build
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

Following variables can be set to custom values, before sourcing Zplugin. The
previous global variables like `$ZPLG_HOME` have been removed to not pollute
the namespace – there's single `$ZPLGM` ("*ZPLUGIN MAP*") hash instead of `8` string
variables. Please update your dotfiles.

```
declare -A ZPLGM  # initial Zplugin's hash definition, if configuring before loading Zplugin, and then:
```
| Hash Field | Description |
-------------|--------------
| ZPLGM[BIN_DIR]         | Where Zplugin code resides, e.g.: "~/.zplugin/bin"                      |
| ZPLGM[HOME_DIR]        | Where Zplugin should create all working directories, e.g.: "~/.zplugin" |
| ZPLGM[PLUGINS_DIR]     | Override single working directory – for plugins, e.g. "/opt/zsh/zplugin/plugins" |
| ZPLGM[COMPLETIONS_DIR] | As above, but for completion files, e.g. "/opt/zsh/zplugin/root_completions"     |
| ZPLGM[SNIPPETS_DIR]    | As above, but for snippets |
| ZPLGM[ZCOMPDUMP_PATH]  | Path to `.zcompdump` file, with the file included (i.e. it's name can be different) |
| ZPLGM[COMPINIT_OPTS]   | Options for `compinit` call (i.e. done by `zpcompinit`), use to pass -C to speed up loading |
| ZPLGM[MUTE_WARNINGS]   | If set to `1`, then mutes some of the Zplugin warnings, specifically the `plugin already registered` warning |

There is also `$ZPFX`, set by default to `~/.zplugin/polaris` – a directory
where software with `Makefile`, etc. can be pointed to, by e.g. `atclone'./configure --prefix=$ZPFX'`.

## Non-Github (Local) Plugins

Use `create` subcommand with user name `_local` (the default) to create plugin's
skeleton in `$ZPLGM[PLUGINS_DIR]`. It will be not connected with Github repository
(because of user name being `_local`). To enter the plugin's directory use `cd` command
with just plugin's name (without `_local`, it's optional).

If user name will not be `_local`, then Zplugin will create repository also on Github
and setup correct repository origin.


## Extending Git

There are several projects that provide git extensions. Installing them with
Zplugin has many benefits:

 - all files are under `$HOME` – no administrator rights needed,
 - declarative setup (like Chef or Puppet) – copying `.zshrc` to different account
   brings also git-related setup,
 - easy update by e.g. `zplugin update --all`.

Below is a configuration that adds multiple git extensions, loaded in Turbo Mode,
two seconds after prompt:

```zsh
zplugin ice wait"2" lucid as"program" pick"bin/git-dsf"
zplugin light zdharma/zsh-diff-so-fancy
zplugin ice wait"2" lucid as"program" pick"$ZPFX/bin/git-now" make"prefix=$ZPFX install"
zplugin light iwata/git-now
zplugin ice wait"2" lucid as"program" pick"$ZPFX/bin/git-alias" make"PREFIX=$ZPFX" nocompile
zplugin light tj/git-extras
zplugin ice wait"2" lucid as"program" atclone'perl Makefile.PL PREFIX=$ZPFX' atpull'%atclone' \
            make'install' pick"$ZPFX/bin/git-cal"
zplugin light k4rthik/git-cal
```

Target directory for installed files is `$ZPFX` (`~/.zplugin/polaris` by default).

# Additional Resources

Besides the main-knowledge source, i.e. this README, there are subpages that are
**guides** and also an external web-page:

 - [Wiki](http://zdharma.org/zplugin/site/)
 - [Zplugin semigraphical dashboard](https://github.com/psprint/zplugin-crasis)

# IRC Channel
Connect to [chat.freenode.net:6697](ircs://chat.freenode.net:6697/%23zplugin) (SSL) or [chat.freenode.net:6667](irc://chat.freenode.net:6667/%23zplugin) and join #zplugin.

Following is a quick access via Webchat [![IRC](https://kiwiirc.com/buttons/chat.freenode.net/zplugin.png)](https://kiwiirc.com/client/chat.freenode.net:+6697/#zplugin)

[status-badge]: https://travis-ci.org/zdharma/zplugin.svg?branch=master
[status-link]: https://travis-ci.org/zdharma/zplugin
[MIT-badge]: https://img.shields.io/badge/license-MIT-blue.svg
[MIT-link]: ./LICENSE
[ver-badge]: https://img.shields.io/github/tag/zdharma/zplugin.svg
[ver-link]: https://github.com/zdharma/zplugin/releases
[act-badge]: https://img.shields.io/github/commit-activity/y/zdharma/zplugin.svg
[lobby-badge]: https://badges.gitter.im/zplugin/Lobby.svg
[lobby-link]: https://gitter.im/zplugin/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge

<!-- vim:tw=87
-->
