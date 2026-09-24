# Runtime Components

# ~/.bashrc — read by bash every time you open an interactive terminal.
#
# After editing, apply your changes to the current terminal with:
#     source ~/.bashrc

# ---------------------------------------------------------------------------
# Stop here if this shell is not interactive.
# ---------------------------------------------------------------------------
# Programs like scp and rsync start a non-interactive bash behind the scenes.
# If this file prints anything in that situation, those programs break.
# This line makes everything below apply only when a human is typing.
[[ $- != *i* ]] && return

# ---------------------------------------------------------------------------
# History
# ---------------------------------------------------------------------------
HISTSIZE=10000              # commands remembered in this session
HISTFILESIZE=20000          # commands saved to ~/.bash_history
HISTCONTROL=ignoreboth      # skip duplicates and lines starting with a space
shopt -s histappend         # add to the history file instead of overwriting it

# ---------------------------------------------------------------------------
# Prompt:   user@host:~/cs240/hw1 (main)$
# ---------------------------------------------------------------------------
# Shows the current git branch when you are inside a repository.
git_branch() {
    local b
    b=$(git symbolic-ref --short HEAD 2>/dev/null) && printf ' (%s)' "$b"
}
PS1='\[\e[32m\]\u@\h\[\e[0m\]:\[\e[34m\]\w\[\e[33m\]$(git_branch)\[\e[0m\]\$ '

# ---------------------------------------------------------------------------
# Aliases — shortcuts for commands you type constantly
# ---------------------------------------------------------------------------
# Colored ls. Linux and macOS spell this option differently.
if ls --color=auto / >/dev/null 2>&1; then
    alias ls='ls --color=auto'
else
    alias ls='ls -G'
fi
alias ll='ls -alF'          # long listing, hidden files, type markers
alias la='ls -A'            # hidden files, without . and ..

# Ask before deleting or overwriting. There is no trash can in a terminal.
alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Compile C with warnings and debug info.
# Named gccw on purpose: it does not replace plain gcc, so you always
# know exactly what flags you are using.
alias gccw='gcc -Wall -Wextra -g'

alias gdb='gdb -q'          # skip the gdb license banner
alias ..='cd ..'
alias grep='grep --color=auto'

# ---------------------------------------------------------------------------
# Environment
# ---------------------------------------------------------------------------
export EDITOR=vim           # editor used by programs that need one
export PAGER=less

# Put your own scripts in ~/bin and run them from anywhere.
if [ -d "$HOME/bin" ]; then
    PATH="$HOME/bin:$PATH"
fi

# ~/.zshrc — read by zsh every time you open an interactive terminal.
#
# zsh is the default shell on macOS. It does NOT read ~/.bashrc.
# After editing, apply your changes to the current terminal with:
#     source ~/.zshrc

# ---------------------------------------------------------------------------
# oh-my-zsh: a framework that adds themes, plugins, and sensible defaults.
# https://ohmyz.sh   (install.sh in this repository installs it for you)
# ---------------------------------------------------------------------------
export ZSH="$HOME/.oh-my-zsh"

# Prompt theme. Browse the options at
#   https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
# robbyrussell (the default) shows the current directory and git branch,
# and turns the arrow red when the last command failed.
ZSH_THEME="robbyrussell"

# Plugins live in $ZSH/plugins. Each one adds features or aliases.
#   git                 ~150 shortcuts, e.g. `gst` for `git status`.
#                       Learn the full commands first; the shortcuts can wait.
#   colored-man-pages   makes `man` pages easier to read
plugins=(git colored-man-pages)

# Remind me about oh-my-zsh updates instead of updating silently.
zstyle ':omz:update' mode reminder

if [ -d "$ZSH" ]; then
    source "$ZSH/oh-my-zsh.sh"
else
    # oh-my-zsh is not installed: use a plain prompt that still shows
    # the git branch, e.g.   user@host:~/cs240/hw1 (main)%
    autoload -Uz vcs_info
    precmd() { vcs_info }
    zstyle ':vcs_info:git:*' formats ' (%b)'
    setopt PROMPT_SUBST
    PROMPT='%F{green}%n@%m%f:%F{blue}%~%f%F{yellow}${vcs_info_msg_0_}%f%# '

    autoload -Uz compinit && compinit    # tab completion
fi

# ---------------------------------------------------------------------------
# History
# ---------------------------------------------------------------------------
HISTFILE=~/.zsh_history
HISTSIZE=10000              # commands remembered in this session
SAVEHIST=20000              # commands saved to ~/.zsh_history
setopt HIST_IGNORE_DUPS     # skip a command identical to the previous one
setopt HIST_IGNORE_SPACE    # skip commands that start with a space
setopt SHARE_HISTORY        # all open terminals share one history

# ---------------------------------------------------------------------------
# Aliases: the same ones as in bashrc, so both shells feel the same
# ---------------------------------------------------------------------------
if ls --color=auto / >/dev/null 2>&1; then
    alias ls='ls --color=auto'
else
    alias ls='ls -G'
fi
alias ll='ls -alF'
alias la='ls -A'

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

alias gccw='gcc -Wall -Wextra -g'
alias gdb='gdb -q'
alias grep='grep --color=auto'

# ---------------------------------------------------------------------------
# Environment
# ---------------------------------------------------------------------------
export EDITOR=vim
export PAGER=less

if [ -d "$HOME/bin" ]; then
    PATH="$HOME/bin:$PATH"
fi
" ~/.vimrc — read by vim every time it starts.
" Lines starting with a double quote are comments.
"
" To see what any option does, run inside vim:   :help 'number'

" ---------------------------------------------------------------------------
" Basics
" ---------------------------------------------------------------------------
set nocompatible            " use vim features, not strict old-vi behavior
syntax on                   " color the code
filetype plugin indent on   " language-aware indentation (C, Python, ...)
set encoding=utf-8
set backspace=indent,eol,start  " backspace works the way you expect

" ---------------------------------------------------------------------------
" What you see
" ---------------------------------------------------------------------------
set number                  " line numbers — compiler errors cite them
set ruler                   " cursor position in the bottom right
set showcmd                 " show partially typed commands
set laststatus=2            " always show the status line
set showmatch               " briefly jump to the matching bracket
set scrolloff=5             " keep 5 lines visible above/below the cursor
set colorcolumn=80          " vertical guide at column 80
set wildmenu                " tab-completion menu for : commands

" ---------------------------------------------------------------------------
" Indentation
" ---------------------------------------------------------------------------
" Match these numbers to your course's code standard.
set tabstop=2               " a tab character displays as 2 columns
set shiftwidth=2            " >> and auto-indent move by 2 columns
set softtabstop=2           " the Tab key inserts 2 columns
set expandtab               " insert spaces, never tab characters
set autoindent              " new lines keep the previous line's indent

" Makefiles REQUIRE real tab characters. Without this line, expandtab above
" would silently break every Makefile you edit.
autocmd FileType make setlocal noexpandtab

" ---------------------------------------------------------------------------
" Searching  ( /word  then  n  for next,  N  for previous )
" ---------------------------------------------------------------------------
set incsearch               " jump to matches while you type
set hlsearch                " highlight all matches
set ignorecase              " case-insensitive search...
set smartcase               " ...unless you type a capital letter

" ---------------------------------------------------------------------------
" Convenience
" ---------------------------------------------------------------------------
" Reopen a file at the line where you last left it.
autocmd BufReadPost *
    \ if line("'\"") > 1 && line("'\"") <= line("$") |
    \   exe "normal! g'\"" |
    \ endif
# ~/.gdbinit — read by gdb every time it starts.

# Remember commands between gdb sessions (use the up arrow, like in bash).
set history save on
set history size 10000
set history filename ~/.gdb_history

# Print structs one field per line instead of one long line.
set print pretty on

# Don't ask "are you sure?" when you quit or restart a running program.
set confirm off

# Don't stop long output with "--Type <RET> for more--".
set pagination off

# Show 20 lines of source around the current line with `list`.
set listsize 20
# ~/.gitconfig: read by git every time it runs.

[init]
	defaultBranch = main

[core]
	editor = vim

[pull]
	rebase = true

[color]
	ui = auto

# Aliases: `git st` runs `git status`, and so on.
[alias]
	st = status
	br = branch
	sw = switch
	last = log -1 HEAD --stat
	# One line per commit, with a branch graph:
	lg = log --oneline --graph --decorate --all
	# Undo `git add file` (keeps your edits, just unstages them):
	unstage = restore --staged --
#!/usr/bin/env bash
#
# install.sh: put the rc files in this repository to work.
#
# Usage:
#   ./install.sh             install everything
#   ./install.sh --dry-run   show what would happen, change nothing
#   ./install.sh --no-omz    skip installing oh-my-zsh
#
# What it does:
#   1. Copies each rc file here into your home directory, e.g.
#        ~/runtime_components/vimrc  ->  ~/.vimrc
#      These are copies: after editing a file in the repo, run this
#      script again to install the new version.
#   2. Copies gitconfig to ~/.gitconfig.rc and tells git to also read it
#      (your name and email stay in ~/.gitconfig, out of this repository).
#   3. Installs oh-my-zsh, if zsh is available.
#
# Safe to run more than once. Nothing is ever deleted: a file already in
# the way is renamed to  ~/.NAME.backup-DATE-TIME  first.

set -eu

# Files in this repository that become ~/.NAME. Missing ones are skipped,
# so add names here as your collection grows.
FILES=".bashrc .zshrc .vimrc .gdbinit"

OMZ_URL="https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh"

# ---------------------------------------------------------------------------
# Options
# ---------------------------------------------------------------------------
DRY_RUN=0
INSTALL_OMZ=1
for arg in "$@"; do
    case "$arg" in
        -n|--dry-run) DRY_RUN=1 ;;
        --no-omz)     INSTALL_OMZ=0 ;;
        -h|--help)    sed -n '3,20p' "$0" | sed 's/^# \{0,1\}//'; exit 0 ;;
        *)            echo "unknown option: $arg  (try --help)" >&2; exit 1 ;;
    esac
done

# Absolute path of the directory holding this script, so it works no matter
# which directory you run it from.
REPO_DIR="$(cd "$(dirname "$0")" && pwd -P)"
STAMP="$(date +%Y%m%d-%H%M%S)"

# Run a command, or just print it during a dry run.
run() {
    if [ "$DRY_RUN" -eq 1 ]; then
        echo "          would run: $*"
    else
        "$@"
    fi
}

# ---------------------------------------------------------------------------
# 1. Copy rc files
# ---------------------------------------------------------------------------
install_file() {
    local src="$REPO_DIR/$1"
    local dest="$HOME/$1"

    if [ ! -e "$src" ]; then
        echo "skip      .$1  (no $1 in this repo)"
        return
    fi

    # Already an identical copy: nothing to do.
    if [ -f "$dest" ] && [ ! -L "$dest" ] && cmp -s "$src" "$dest"; then
        echo "ok        .$1"
        return
    fi

    # A different file is in the way: move it aside, never delete it.
    if [ -e "$dest" ] || [ -L "$dest" ]; then
        local bak="$dest.backup-$STAMP" n=1
        while [ -e "$bak" ] || [ -L "$bak" ]; do   # never overwrite a backup
            bak="$dest.backup-$STAMP-$n"; n=$((n + 1))
        done
        echo "backup    .$1  ->  ${bak##*/}"
        run mv "$dest" "$bak"
    fi

    echo "copy      $1  ->  ~/$1"
    run cp "$src" "$dest"
}

# ---------------------------------------------------------------------------
# 2. gitconfig: include it, don't replace ~/.gitconfig
# ---------------------------------------------------------------------------
# ~/.gitconfig holds your name and email. Replacing it would lose them.
# Instead, gitconfig is copied to ~/.gitconfig.rc, and ~/.gitconfig gets
# one extra line telling git to also read that file.
setup_gitconfig() {
    local src="$REPO_DIR/.gitconfig"
    local dest="$HOME/.gitconfig.rc"

    if [ ! -e "$src" ]; then
        echo "skip      gitconfig  (no gitconfig in this repo)"
        return
    fi

    if git config -f "$src" --get user.name >/dev/null 2>&1 ||
       git config -f "$src" --get user.email >/dev/null 2>&1; then
        echo "WARNING   $src has a [user] section."
        echo "          It would override the name and email in ~/.gitconfig."
        echo "          Remove it from the repo copy, then run this script again."
        return
    fi

    if [ -f "$dest" ] && cmp -s "$src" "$dest"; then
        echo "ok        .gitconfig.rc"
    else
        echo "copy      .gitconfig  ->  ~/.gitconfig.rc"
        run cp "$src" "$dest"
    fi

    if git config --global --get-all include.path 2>/dev/null | grep -qxF "$dest"; then
        echo "ok        .gitconfig.rc  (already included)"
    else
        echo "include   .gitconfig.rc  (added to ~/.gitconfig)"
        run git config --global --add include.path "$dest"
    fi
}

# ---------------------------------------------------------------------------
# 3. oh-my-zsh
# ---------------------------------------------------------------------------
install_omz() {
    if ! command -v zsh >/dev/null 2>&1; then
        echo "skip      oh-my-zsh  (zsh is not installed)"
        echo "          Ubuntu/WSL: sudo apt install zsh     macOS: already installed"
        return
    fi

    if [ -d "${ZSH:-$HOME/.oh-my-zsh}" ]; then
        echo "ok        oh-my-zsh  (already installed)"
        return
    fi

    local fetch
    if command -v curl >/dev/null 2>&1; then
        fetch="curl -fsSL"
    elif command -v wget >/dev/null 2>&1; then
        fetch="wget -qO-"
    else
        echo "skip      oh-my-zsh  (needs curl or wget)"
        return
    fi

    echo "install   oh-my-zsh"
    if [ "$DRY_RUN" -eq 1 ]; then
        echo "          would download and run $OMZ_URL"
        return
    fi

    # Download first, then run, so a failed download is reported instead of
    # silently doing nothing. You can read the installer before running it.
    local tmp
    tmp="$(mktemp "${TMPDIR:-/tmp}/omz-install.XXXXXX")"
    if ! $fetch "$OMZ_URL" > "$tmp"; then
        echo "ERROR     could not download oh-my-zsh. Check your connection."
        rm -f "$tmp"
        return
    fi

    # --unattended : don't change your shell and don't start zsh.
    # KEEP_ZSHRC   : don't overwrite the ~/.zshrc we just copied.
    KEEP_ZSHRC=yes sh "$tmp" --unattended
    rm -f "$tmp"
}

# ---------------------------------------------------------------------------
# Go
# ---------------------------------------------------------------------------
echo "Installing from $REPO_DIR"
[ "$DRY_RUN" -eq 1 ] && echo "(dry run: nothing will be changed)"
echo

for f in $FILES; do
    install_file "$f"
done

setup_gitconfig

if [ "$INSTALL_OMZ" -eq 1 ]; then
    install_omz
fi

echo
echo "Done. Open a new terminal to load everything."

case "${SHELL:-}" in
    */zsh) ;;
    *)
        if command -v zsh >/dev/null 2>&1; then
            echo
            echo "Your login shell is ${SHELL:-unknown}, so zshrc will not load yet."
            echo "To switch to zsh:  chsh -s \"\$(command -v zsh)\""
        fi
        ;;
esac
$ ls -al
total 44
drwxr-xr-x 3 kameranis kameranis 4096 Sep 21 12:45 .
drwxr-xr-x 4 kameranis kameranis 4096 Sep 21 08:19 ..
-rw-r--r-- 1 kameranis kameranis 2823 Sep 21 08:29 .bashrc
-rw-r--r-- 1 kameranis kameranis  534 Sep 21 08:29 .gdbinit
drwxr-xr-x 7 kameranis kameranis 4096 Sep 21 12:40 .git
-rw-r--r-- 1 kameranis kameranis  721 Sep 21 10:13 .gitconfig
-rw-r--r-- 1 kameranis kameranis 6605 Sep 21 12:36 install.sh
-rw-r--r-- 1 kameranis kameranis 3489 Sep 21 12:44 README.md
-rw-r--r-- 1 kameranis kameranis 2827 Sep 21 08:29 .vimrc
-rw-r--r-- 1 kameranis kameranis 2958 Sep 21 12:30 .zshrc
git status
git add .
git commit -m "Initial commit."
git status
