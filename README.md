# bash-powerline

Easily customizable Powerline for Bash in pure Bash script.

![bash-powerline](https://raw.github.com/riobard/bash-powerline/master/screenshots/solarized-light-source-code-pro.png)
![bash-powerline](https://raw.github.com/riobard/bash-powerline/master/screenshots/solarized-dark-monaco.png)

## Features

* Platform-dependent prompt symbol for OS X and Linux (see screenshots)
* Exit code if the previous command failed
* Fast execution (no noticable delay)
* Easy customization (see below)


## Installation

Download the Bash script

    curl https://raw.githubusercontent.com/ricochet1k/bash-powerline/master/bash-powerline.sh > ~/.bash-powerline.sh

And source it in your `.bashrc`

    source ~/.bash-powerline.sh

For best result, use [Solarized colorscheme](https://github.com/altercation/solarized) for your terminal
emulator. Or hack your own colorscheme by modifying the script. It's really
easy.


## Customization
Start by copying the `powerline_prompt` function in the script into your
.bashrc
```bash
powerline_prompt() {
  local ret=$?
  if [ $ret -ne 0 ]; then
    powerline_segment BASE03 WHITE "$BOLD$ret"
  fi
  powerline_segment BLUE WHITE "$BOLD$USER"
  powerline_segment BASE01 WHITE "$BOLD$(dirs +0)" # show $PWD relative to ~
  powerline_segment BLACK NONE ""
}
```

Put this after the source command. There are 3, maybe 4 segments here. The first
one is the return value of the previous command, but only if it is not 0. Then
you have your username, your current directory, and a final empty segment to
reset to black.

Each powerline_segment takes background, foreground and text.

### Example segments
Current time:
```bash
powerline_segment BASE02 WHITE "$(date +%X)"
```

Current git branch and how many ahead/behind (stolen from original fork):
```bash
__git_info() { 
    [ -x "$(which git)" ] || return    # git not found

    local git_eng="env LANG=C git"   # force git output in English to make our work easier
    # get current branch name or short SHA1 hash for detached head
    local branch="$($git_eng symbolic-ref --short HEAD 2>/dev/null || $git_eng describe --tags --always 2>/dev/null)"
    [ -n "$branch" ] || return  # git branch not found

    local marks

    # branch is modified?
    [ -n "$($git_eng status --porcelain)" ] && marks+=" $GIT_BRANCH_CHANGED_SYMBOL"

    # how many commits local branch is ahead/behind of remote?
    local stat="$($git_eng status --porcelain --branch | grep '^##' | grep -o '\[.\+\]$')"
    local aheadN="$(echo $stat | grep -o 'ahead [[:digit:]]\+' | grep -o '[[:digit:]]\+')"
    local behindN="$(echo $stat | grep -o 'behind [[:digit:]]\+' | grep -o '[[:digit:]]\+')"
    [ -n "$aheadN" ] && marks+=" $GIT_NEED_PUSH_SYMBOL$aheadN"
    [ -n "$behindN" ] && marks+=" $GIT_NEED_PULL_SYMBOL$behindN"

    # print the git branch segment without a trailing newline
    printf " $GIT_BRANCH_SYMBOL$branch$marks "
}

powerline_segment BASE00 WHITE "$(__git_info)"
```

## Why?
I made this fork of [bash-powerline](https://github.com/riobard/bash-powerline/)
because I wanted an easily customizable powerline implementation for bash.
Modifying random JSON files and python scripts for something as simple as adding
a segment for the current time should not require reading a ton of documentation
and debugging.

And I like patched fonts.

## See also
* [powerline](https://github.com/Lokaltog/powerline): Unified Powerline
  written in Python. This is the future of all Powerline derivatives. 
* [vim-powerline](https://github.com/Lokaltog/vim-powerline): Powerline in Vim
  writtien in pure Vimscript. Deprecated.
* [tmux-powerline](https://github.com/erikw/tmux-powerline): Powerline for Tmux
  written in Bash script. Deprecated.
* [powerline-shell](https://github.com/milkbikis/powerline-shell): Powerline for
  Bash/Zsh/Fish implemented in Python. Might be merged into the unified
  Powerline. 
* [emacs powerline](https://github.com/milkypostman/powerline): Powerline for
  Emacs
