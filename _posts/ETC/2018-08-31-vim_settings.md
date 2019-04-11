---
layout: post
title:  "vim 설정"
date:   2018-08-31
category: "E.T.C."
tags: [vim, neovim]
---
# install neovim
## OSX
```bash
brew install neovim/neovim/neovim
```
## Linux
```bash
apt-add-repository ppa:neovim-ppa/stable
apt-get update
apt-get install neovim
apt-get install python-dev python-pip python3-dev python3-pip
pip3 install --user neovim
```

# alias 추가
* vi 대신 nvim을 쓰기위해...
```
mkdir -p ${XDG_CONFIG_HOME:=$HOME/.config}
mkdir ~/.vim
touch ~/.vimrc
ln -s ~/.vim $XDG_CONFIG_HOME/nvim
ln -s ~/.vimrc $XDG_CONFIG_HOME/nvim/init.vim
```

* zshrc나 bashrc에 적절히 추가
```
alias vi="nvim"
```

---

# install `node`, `yarn`
## OSX
```bash
brew upgrade node
brew install yarn
```

## Linux
```
curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
apt-get install nodejs

curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
apt-get update
apt-get install yarn
```

---

# install Vundle and setup vimrc

## install Vundle

```
git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

## vimrc
* neovim이 아닌 경우 다음 두가지를 수정해주어야한다.
    * `set rtp+=~/.config/nvim/bundle/Vundle.vim -> ~/.vim/bundle/Vundle.vim`
    * `call vundle#begin('~/.config/nvim/bundle') -> call vundle#begin()`

```
" Vundle Setting "
set nocompatible
filetype off

set rtp+=~/.config/nvim/bundle/Vundle.vim
call vundle#begin('~/.config/nvim/bundle')

Plugin 'VundleVim/Vundle.vim'
Plugin 'scrooloose/nerdtree'
Plugin 'cocopon/iceberg.vim'
Plugin 'neoclide/coc.nvim'

call vundle#end()

filetype plugin indent on    " required

set nu

if has("autocmd")
  au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
endif

" Syntax Highlighting "
syntax on

set autoindent                      " 이전 라인 들여쓰기 하면 개행시 다음 라인에도 자동 적용 "
set smartindent                     " autoindent 와 비슷 몇몇 c언어의 문법에 대해 좀더 똑똑하게 들여 쓰기를 적용 "
set cindent                         " 좀더 똑똑하게 c 언어의 문법에 맞게 들여 쓰기 적용 "

set scrolloff=4                     " 스크롤 할때 위아래 줄 유지, 화살표 아래로 내리다 보면 아래에서 4줄 유지한 상황에서 스크롤 됨 "
set tabstop=4                       " 탭을 4칸 "
set softtabstop=4                   " tab키를 입력 하였을때 입력될 너비 "
set shiftwidth=4                    " 자동 들여쓰기 4칸 "
set shiftround                      " "
set expandtab                       " 탭대신 스페이스 "

set textwidth=100                   " 글자수 100자 이상이면 자동 개행 "

set hlsearch                        " / ? 명령등으로 찾은 문자열을 하이라이트"
set incsearch                       " 글자를 치면 그에 맞는것을 치는 동안 보여주기 "

filetype on                         " 파일 종류에 따른 구문을 강조 표시 "

set ls=2                            " 항상 status 라인을 표시 "

set ut=300000                       " 몇 milliseconds 후에 swap 파일 생성 5분 "
set uc=100                          " 몇 글자 이상 써야 swap 파일 생성 "

" For python http://www.vim.org/scripts/script.php?script_id=790 "
let python_version_2=1              " python 2 문법"
let python_highlight_all=1          " 모든 강조 기능을 on"

" Color scheme
if !has('gui_running')
    set t_Co=256
endif

colorscheme iceberg
let g:lightline = { 'colorscheme': 'iceberg' }
set laststatus=2
set noshowmode

" Key Map "

" NERDTree "
nmap <S-n> :NERDTree<CR>

""""""""""""""""" COC """""""""""""""
" if hidden is not set, TextEdit might fail.
set hidden

" Some server have issues with backup files, see #649
set nobackup
set nowritebackup

" Better display for messages
set cmdheight=2

" Smaller updatetime for CursorHold & CursorHoldI
set updatetime=300

" don't give |ins-completion-menu| messages.
set shortmess+=c

" always show signcolumns
set signcolumn=yes

" Use tab for trigger completion with characters ahead and navigate.
" Use command ':verbose imap <tab>' to make sure tab is not mapped by other plugin.
inoremap <silent><expr> <TAB>
      \ pumvisible() ? "\<C-n>" :
      \ <SID>check_back_space() ? "\<TAB>" :
      \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"

function! s:check_back_space() abort
  let col = col('.') - 1
  return !col || getline('.')[col - 1]  =~# '\s'
endfunction

" Use <c-space> for trigger completion.
inoremap <silent><expr> <c-space> coc#refresh()

" Use <cr> for confirm completion, `<C-g>u` means break undo chain at current position.
" Coc only does snippet and additional edit on confirm.
inoremap <expr> <cr> pumvisible() ? "\<C-y>" : "\<C-g>u\<CR>"

" Remap keys for gotos
nmap <silent> gd <Plug>(coc-definition)
nmap <silent> gy <Plug>(coc-type-definition)
nmap <silent> gi <Plug>(coc-implementation)
nmap <silent> gr <Plug>(coc-references)

" Use K for show documentation in preview window
nnoremap <silent> K :call <SID>show_documentation()<CR>

function! s:show_documentation()
  if &filetype == 'vim'
    execute 'h '.expand('<cword>')
  else
    call CocAction('doHover')
  endif
endfunction

" Highlight symbol under cursor on CursorHold
autocmd CursorHold * silent call CocActionAsync('highlight')

" Remap for rename current word
nmap <leader>rn <Plug>(coc-rename)

" Remap for format selected region
vmap <leader>f  <Plug>(coc-format-selected)
nmap <leader>f  <Plug>(coc-format-selected)

augroup mygroup
  autocmd!
  " Setup formatexpr specified filetype(s).
  autocmd FileType typescript,json setl formatexpr=CocAction('formatSelected')
  " Update signature help on jump placeholder
  autocmd User CocJumpPlaceholder call CocActionAsync('showSignatureHelp')
augroup end

" Remap for do codeAction of selected region, ex: `<leader>aap` for current paragraph
vmap <leader>a  <Plug>(coc-codeaction-selected)
nmap <leader>a  <Plug>(coc-codeaction-selected)

" Remap for do codeAction of current line
nmap <leader>ac  <Plug>(coc-codeaction)
" Fix autofix problem of current line
nmap <leader>qf  <Plug>(coc-fix-current)

" Use `:Format` for format current buffer
command! -nargs=0 Format :call CocAction('format')

" Use `:Fold` for fold current buffer
command! -nargs=? Fold :call     CocAction('fold', <f-args>)


" Add diagnostic info for https://github.com/itchyny/lightline.vim
let g:lightline = {
      \ 'colorscheme': 'wombat',
      \ 'active': {
      \   'left': [ [ 'mode', 'paste' ],
      \             [ 'cocstatus', 'readonly', 'filename', 'modified' ] ]
      \ },
      \ 'component_function': {
      \   'cocstatus': 'coc#status'
      \ },
      \ }


" Using CocList
" Show all diagnostics
nnoremap <silent> <space>a  :<C-u>CocList diagnostics<cr>
" Manage extensions
nnoremap <silent> <space>e  :<C-u>CocList extensions<cr>
" Show commands
nnoremap <silent> <space>c  :<C-u>CocList commands<cr>
" Find symbol of current document
nnoremap <silent> <space>o  :<C-u>CocList outline<cr>
" Search workspace symbols
nnoremap <silent> <space>s  :<C-u>CocList -I symbols<cr>
" Do default action for next item.
nnoremap <silent> <space>j  :<C-u>CocNext<CR>
" Do default action for previous item.
nnoremap <silent> <space>k  :<C-u>CocPrev<CR>
" Resume latest coc list
nnoremap <silent> <space>p  :<C-u>CocListResume<CR>
```

# PluginInstall

* vi에 들어가서 
    * `:PluginInstall`
    * `:call coc#util#install()`
    * `:CocInstall coc-python`

# jedi, neovim install
* `pip install neovim jedi`
