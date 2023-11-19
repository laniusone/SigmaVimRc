# SigmaVimRc

**BREAKING**
SigmaVimRc is now ShrikeVim. The `master` branch will remain untouched if you wanna
keep using it, but if you do it's recommended to just yoink the the config. To switch to
ShrikeVim some changes will be needed, you might want to re-add some elements e.g.: nnn
integration that was dropped. You'll do that just the same as you did here.
New main branch is... `main`. Also the repository address has changed and it should
be updated in your config.

![](https://imgur.com/4oOuxJ7.png)

<!--toc:start-->

- [SigmaVimRc](#sigmavimrc)
  - [Why another Vim distribution](#why-another-vim-distribution)
  - [What is Sigma](#what-is-sigma)
  - [Dependencies](#dependencies)
  - [Installation](#installation)
  - [Configuration](#configuration)
    - [FZF](#fzf)
    - [nnn](#nnn)
    - [lazygit](#lazygit)
    - [Overriding configuration](#overriding-configuration)
    - [Plugins](#plugins)
    - [Extra functions](#extra-functions)
    - [LSP](#lsp)
  - [Default plugins](#default-plugins)
  - [Keybindings](#keybindings)
  - [Features](#features)
  - [Known issues](#known-issues)
    - [Cursor line gets lost](#cursor-line-gets-lost)
  - [Roadmap](#roadmap)
  <!--toc:end-->

You might have already seen NvChad, but you use Vim and want to be a Chad too?
SigmaVimRC was made for you.

## Why another Vim distribution

First of all, most of "Vim" distros, are Neovim distros. SigmaVimRc is an actual
Vim distribution (if we want to call it like this).

Most of these distributions are pretty complex, and happen to be a pain to
actually replace some base plugins.

## What is Sigma

Sigma is meant to be simple. It uses vim-plug as plugin manager, which
is also wrapped in sigma#add, sigma#remove and sigma#init functions,
which allows full control over what plugins from base set are actually
used.

Sigma comes with sane (or maybe opinionated?) defaults and utilities to
make your vimrc as small and readable as possible.

Sigma comes with kyotonight.vim theme, which might easily be changed to
any theme of your choice. There is also a plan of adding a possibility
to create your own colorscheme providing just color palette.

Sigma uses fzf which powers all the fuzzy finding in the default config.
Check the recommended config section for FZF.

Sigma uses nnn as file picker and file browser in place of netrw, it
also doesn't provide any file-tree plugin.

Rationale for this choices is just... keeping it simple. Use the same
tools inside of Vim which you would use outside of Vim. Fzf is great all
around fuzzy finder. Nnn is a full blown, fully functional file manager.
No file browser plugin can compare with that. And again, it's a tool
for everyday use, not just in Vim.

If you prefer different workflow, replacing nnn.vim with a file-tree
plugin of choice is just removing one plugin, adding another one, and
replacing 2 remaps. You can also add a file-tree plugin on top of nnn,
so you might just want to replace only one remap, `<C-n>` with toggling
your file-tree.

Sigma comes with vim-fugitive, but it's solely for hunks in lightline. Intended
git workflow is using lazygit in a kitty, tmux or Vim terminal pane opened by
`<leader>gg` keybinding. It follows the same rationale of using tools usable
outside of Vim, but you can easily install / use your preferred plugin for git,
as the actual lazygit integration in Sigma is this single keybinding for opening
lazygit in current working directory. There's no plugin for that in the base set.

In the end, you might always take parts of SigmaVimRc as inspiration for
making your own config, which is also a valid use case for this little
project. :)

## Dependencies

- [NerdFont](https://github.com/ryanoasis/nerd-fonts)
- [Fzf](https://github.com/junegunn/fzf) (All the fuzzy finding)
- [ripgrep](https://github.com/BurntSushi/ripgrep) (For Fzf default
  config in Sigma)
- [fd](https://github.com/sharkdp/fd) (Also for Fzf)
- [nnn](https://github.com/jarun/nnn) (Default file picker, might be
  disabled / replaced)
- [npm](https://github.com/npm/cli) (For coc.nvim)
- [kitty](https://github.com/kovidgoyal/kitty) or
  [tmux](https://github.com/tmux/tmux) (optional)
- [lazygit](https://github.com/jesseduffield/lazygit) (For... lazygit
  integration)
- [python3](https://www.python.org/) (Ultisnips)

## Installation

Install vim-plug first:

```sh
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Install SigmaVimRc:

```sh
curl -fLo ~/.vim/autoload/sigma.vim --create-dirs \
    https://raw.githubusercontent.com/sigmavim/vimrc/master/autoload/sigma.vim
```

## Configuration

To use the default SigmaVimRc just add this line to your .vimrc:

```vim
call sigma#init()
```

### FZF

Recommended options for fzf are as follows:

```sh
# fzf default command
export FZF_DEFAULT_COMMAND="rg -g '!{.git,node_modules,.composer}/' --hidden --no-ignore -l ''"
# fzf theming
export FZF_DEFAULT_OPTS=$FZF_DEFAULT_OPTS' --color=fg:#a9b1d6,bg:#1a1b26,hl:#7aa2f7 --color=fg+:#c0caf5,bg+:#1a1b26,hl+:#7dcfff --color=info:#cfc9c2,prompt:#f7768e,pointer:#bb9af7 --color=marker:#9ece6a,spinner:#bb9af7,header:#73daca'
```

You should put it in your shell configuration (.bashrc/.zshrc or
.profile/.zshenv)

If Vim is started by `-e` option of terminal emulator, it might not
inherit your shell env, so you might need to add the same options in
your .vimrc:

```vim
let $FZF_DEFAULT_COMMAND = "rg -g '!{.git,node_modules,.composer}/' --hidden --no-ignore -l ''"
let $FZF_DEFAULT_OPTS = '--color=fg:#a9b1d6,bg:#1a1b26,hl:#7aa2f7 --color=fg+:#c0caf5,bg+:#1a1b26,hl+:#7dcfff --color=info:#cfc9c2,prompt:#f7768e,pointer:#bb9af7 --color=marker:#9ece6a,spinner:#bb9af7,header:#73daca'
```

Should you want to customize your fzf theme, e.g. to match colorscheme
you've chosen instead of the default kyotonight.vim (why would you do
that? :( ) check out [this site](https://minsw.github.io/fzf-color-picker/).

### nnn

Since nnn file manager is a big part of intended SigmaVimRc workflow,
below is the recommended configuration (note: preview-tui plugin
requires kitty or tmux):

```sh
# nnn config
BLK="0B" CHR="0B" DIR="04" EXE="06" REG="00" HARDLINK="06" SYMLINK="06" MISSING="00" ORPHAN="09" FIFO="06" SOCK="0B" OTHER="06"
export NNN_FCOLORS="$BLK$CHR$DIR$EXE$REG$HARDLINK$SYMLINK$MISSING$ORPHAN$FIFO$SOCK$OTHER"
export NNN_PLUG='p:/usr/share/nnn/plugins/preview-tui;f:/usr/share/nnn/plugins/fzopen'
```

Colors configuration should be quite universal, as it will use your
terminal's colors, so if you use a theme matching your Vim colorscheme,
everything would look right. Also be sure to tweak the nnn plugins paths
to match the ones on your system. If nnn package on your system doesn't
come with its plugins, you can download them [here](https://github.com/jarun/nnn/tree/master/plugins).

### lazygit

Only thing that is needed here is having lazygit installed. The `<leader>gg`
keybinding pulls up a pane with lazygit in current working directory.

### Overriding configuration

To override default Sigma configuration... just set your configuration
changes after calling sigma#init function. That includes plugin
configurations.

### Plugins

To remove a plugin:

```vim
call sigma#remove('plugin/name')
```

Add a plugin:

```vim
call sigma#add('plugin/name', {'branch': 'master'}) " optional params
```

These functions need to be called **BEFORE** _sigma#init_.

### Extra functions

- sigma#run - opens a split in kitty or tmux, running a supplied
  command in current working directory, or if no command is supplied it
  just opens the terminal. If Vim is run in an unsupported terminal, it
  just opens Vim terminal

1. Examples

   ```vim
   " Run lazygit in current working directory
   nnoremap <silent><leader>gg <Cmd>call sigma#run("lazygit -p")<CR>
   " Open terminal in current working directory
   nnoremap <silent><leader>tt <Cmd>call sigma#run()<CR>
   ```

### LSP

Sigma provides a choice to include CoC.
To use it, put this **BEFORE** calling `sigma#init` function:

```vim
let g:sigma#use_coc = 1
```

Sigma also provides its custom CoC diagnostics support for lightline.

## Default plugins

- [sigmavim/kyotonight](https://github.com/sigmavim/kyotonight)
- [sigmavim/skeleton](https://github.com/sigmavim/skeleton)
- [907th/vim-auto-save](https://github.com/907th/vim-auto-save)
- [tpope/vim-abolish](https://github.com/tpope/vim-abolish)
- [eshion/vim-sync](https://github.com/eshion/vim-sync)
- [leafOfTree/vim-project](https://github.com/leafOfTree/vim-project)
- [lambdalisue/suda.vim](https://github.com/lambdalisue/suda.vim)
- [tpope/vim-commentary](https://github.com/tpope/vim-commentary)
- [skywind3000/asyncrun.vim](https://github.com/skywind3000/asyncrun.vim)
- [mcchrish/nnn.vim](https://github.com/mcchrish/nnn.vim)
- [mbbill/undotree](https://github.com/mbbill/undotree)
- [noahfrederick/vim-skeleton](https://github.com/noahfrederick/vim-skeleton)
- [ryanoasis/vim-devicons](https://github.com/ryanoasis/vim-devicons)
- [BourgeoisBear/clrzr](https://github.com/BourgeoisBear/clrzr)
- [mhinz/vim-startify](https://github.com/mhinz/vim-startify)
- [itchyny/lightline.vim](https://github.com/itchyny/lightline.vim)
- [mengelbrecht/lightline-bufferline](https://github.com/mengelbrecht/lightline-bufferline)
- [sineto/lightline-hunks](https://github.com/sineto/lightline-hunks)
- [airblade/vim-gitgutter](https://github.com/airblade/vim-gitgutter)
- [junnegunn/fzf.vim](https://github.com/junnegunn/fzf.vim)
- [tpope/vim-fugitive](https://github.com/tpope/vim-fugitive)
- [junegunn/vim-peekaboo](https://github.com/junegunn/vim-peekaboo)
- [machakann/vim-highlightedyank](https://github.com/machakann/vim-highlightedyank)
- [honza/vim-snippets](https://github.com/honza/vim-snippets)
- [neoclice/coc.nvim](https://github.com/neoclice/coc.nvim)

## Keybindings

Check [sigma#mappings()](https://github.com/voidekh/SigmaVimRc/blob/master/autoload/sigma.vim#L47)
function, and also review some of the default mappings of listed
plugins.

Some of them might've been borrowed from NvChad or ThePrimeagen.

## Features

- Dashboard with useful shortcuts
- Good looking, easy on the eyes theme
- Code commenting plugins
- Simple project management
- Privilege elevating when editing system config
- Fuzzy finder
- Fancy yet useful status and bufferline
- Autocompletion and snippets
- LSP support
- Undotree
- Nnn file manager integration
- Easy shortcuts to lazygit and full fledged terminal (kitty or tmux required)
- Auto-save
- Easy to configure remote sync
- Undotree
- Better search and replace
- Cheat.sh support
- Color strings highlighting
- Clipboard history picker

## Known issues

### Cursor line gets lost

For some reason `nocursorline` option gets set **sometimes**. I
haven't tracked the issue yet. A workaround (if you want cursorline
that is) is adding following autocmd to your init.vim:

```vim
autocmd FileType * if &ft != 'startify' && &ft != 'dashboard' | :set cursorline | endif
```

## Roadmap

- [x] Add screenshots
- [x] Documentation (and default keybindings info)
- [x] Figure out a way for vim-plug to manage SigmaVimRc or let it
      manage itself
- [x] Features explanation
- [x] Add vim-signify / gitsigns support for lightline
- [ ] Dynamic theme plugin
- [x] New installation method (bootstrap SigmaVimRc and let vim-plug
      manage the full plugin)
- [x] Remove all Neovim specific stuff
