---
layout: post
title:  "vim 설정"
date:   2018-08-31
category: "E.T.C."
tags: [vim, neovim]
---

# 설치 순서
* neovim
* Vundle
* vimrc 설정

# neovim 설치
* osx의 경우
    * `brew install neovim/neovim/neovim`
* ubuntu의 경우
    * `apt-add-repository ppa:neovim-ppa/stable`
    * `apt-get update`
    * `apt-get install neovim`
    * `apt-get install python-dev python-pip python3-dev python3-pip`
    * `pip3 install --user neovim`
    * 다음 alias 추가
        * `alias vi="nvim"`
        * `alias vim="nvim"`

더 좋다고 해서 깔긴하지만, 필수는 아니다.

neovim을 깔 경우 .vimrc와 .vim 폴더가 다르기 때문에 폴더를 alias해주어야한다.
* `mkdir -p ${XDG_CONFIG_HOME:=$HOME/.config}`
* `ln -s ~/.vim $XDG_CONFIG_HOME/nvim`
* `ln -s ~/.vimrc $XDG_CONFIG_HOME/nvim/init.vim`

---

# Vundle 설치
Vundle은 vim plugin을 쉽게 설치하고 관리하게 해주는 툴이다.

`git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim`

## .vimrc에 설정 등록

```vim
" Vundle Setting "
set nocompatible
filetype off

set rtp+=~/.config/nvim/bundle/Vundle.vim 
call vundle#begin('~/.config/nvim/bundle')

Plugin 'VundleVim/Vundle.vim'
Plugin 'Valloric/YouCompleteMe'
Plugin 'scrooloose/nerdtree'
Plugin 'davidhalter/jedi-vim'
Plugin 'cocopon/iceberg.vim'

call vundle#end()

filetype plugin indent on    " required
```

* neovim이 아닌 경우 다음 두가지를 수정해주어야한다.
  * `set rtp+=~/.config/nvim/bundle/Vundle.vim` -> `~/.vim/bundle/Vundle.vim`
  * `call vundle#begin('~/.config/nvim/bundle')` -> `call vundle#begin()`

## plugin 설치

* 위의 vimrc에서
* `Plugin 'scrooloose/nerdtree'`는 NERDTree plugin을 install 해서 쓰겠다는 것이다.
* 이처럼 설치할 플러그인을 명시하고
* vi에 들어가서 :PluginInstall하면
* 설치가 된다.
* 참조
  * :PluginList       - lists configured plugins
  * :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
  * :PluginSearch foo - searches for foo; append `!` to refresh local cache
  * :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal

---

## neovim jedi를 python 버전에 맞게 설치?
pip install neovim jedi


https://github.com/davidhalter/jedi-vim

---
# 최종 vimrc
```vim
" Vundle Setting "
set nocompatible
filetype off

set rtp+=~/.config/nvim/bundle/Vundle.vim
call vundle#begin('~/.config/nvim/bundle')

Plugin 'VundleVim/Vundle.vim'
Plugin 'Valloric/YouCompleteMe'
Plugin 'scrooloose/nerdtree'
Plugin 'davidhalter/jedi-vim'
Plugin 'cocopon/iceberg.vim'

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

" Python Tab-complete http://www.vim.org/scripts/script.php?script_id=850 "
filetype plugin on
let g:pydiction_location='~/.vim/pydiction'
let g:pydiction_menu_height=3

" Show Function name"
fun! ShowFuncName()
    let lnum = line(".")
    let col = col(".")
    echohl ModeMsg
    echo getline(search("^[^ \t#/]\\{2}.*[^:]\s*$", 'bW'))
    echohl None
    call search("\\%" . lnum . "l" . "\\%" . col . "c")
endfun

" Color scheme
if !has('gui_running')
    set t_Co=256
endif

colorscheme iceberg
let g:lightline = { 'colorscheme': 'iceberg' }
set laststatus=2
set noshowmode

" Key Map "
map f :call ShowFuncName() <CR>

" tab move "
nmap <C-t> :tabe<CR> 
nmap <C-p> :tabprevious<CR>
nmap <C-n> :tabnext<CR>

" NERDTree "
nmap <S-n> :NERDTree<CR>

" Jedi "
let g:jedi#popup_on_dot = 0                       " .찍었을 때 popup하지 않도록 "
let g:jedi#completions_command = "<S-Tab>"        " auto completion 단축키 변경 "
set splitbelow                                    " 아랫쪽에 doc string을 보여줌 "

" clipboard 공유
set clipboard=unnamedplus
```
---
# ycmd server SHUT DOWN에러가 나면...
```bash
cd ~.vim/bundle/YouCompleteMe
./install.py
```
