# Plugins (Neovim)

## Installing plugins

- Install vim-plug:
    ```bash
    $ curl -fLo ~/.local/share/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
    ```
- Create a file `~/.config/nvim/init.vim`
- Paste (example):

```vim
call plug#begin('~/.local/share/nvim/site/plugged')

" File System Explorer
Plug 'scrooloose/nerdtree'

" Makes NERDTree behave like a sidebar
Plug 'jistr/vim-nerdtree-tabs'

" Lightweight status line/tab line
Plug 'itchyny/lightline.vim'

" Fuzzy file searching
Plug 'junegunn/fzf'
Plug 'junegunn/fzf.vim'

" Denite-based custom interface
Plug 'Shougo/denite.nvim'

" IntelliSense engine using Neovim's built-in LSP
Plug 'neoclide/coc.nvim', {'branch': 'release'}

" Asynchronous linting/fixing & LSP integration
Plug 'dense-analysis/ale'

" Git wrapper for Vim
Plug 'tpope/vim-fugitive'

" Shows git diff in the sign column
Plug 'airblade/vim-gitgutter'

" Snippet solution with dynamic placeholders
Plug 'SirVer/ultisnips'

" Contains snippets for many languages
Plug 'honza/vim-snippets'

" A collection of language packs for syntax highlighting
Plug 'sheerun/vim-polyglot'

" Extends Vim's native text objects
Plug 'wellle/targets.vim'

" Deals with pairs of surroundings (parentheses, quotes, etc.)
Plug 'tpope/vim-surround'

" Enhanced JavaScript syntax support
Plug 'pangloss/vim-javascript'

" TypeScript syntax support
Plug 'leafgarland/typescript-vim'

" Rust language support
Plug 'rust-lang/rust.vim'

" Autocompletion and static analysis for Python
Plug 'davidhalter/jedi-vim'

" Enhanced Python syntax highlighting
Plug 'vim-python/python-syntax'

" Enhanced C++ syntax highlighting (choose one of the three or use them in tandem if needed)
Plug 'vim-jp/cpp-vim'

" Sidebar to display ctags-generated tags of the current file
Plug 'preservim/tagbar'

" Enhanced Go development support
Plug 'fatih/vim-go'

" Autocompletion for Java
Plug 'artur-shaik/vim-javacomplete2'

" Syntax highlighting for Haskell (sometimes used for Java)
Plug 'neovimhaskell/haskell-vim'

" Runs your Vim tests (for Makefile support)
Plug 'skalnik/vim-vroom'

call plug#end()
```

- Start nvim and run `:PlugInstall`

## Updating plugins**

- `:PlugStatus`
- `:PlugUpdate`
