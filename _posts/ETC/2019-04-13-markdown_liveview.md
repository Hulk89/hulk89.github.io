---
layout: post
title:  "Livedown: vim markdown preview plugin"
date:   2019-04-13
category: "E.T.C."
tags: [vim, markdown, plugin]
---

## Installation

`~/.vimrc`에 다음 추가 후 `PluginInstall`.

```bash
Plugin 'shime/vim-livedown'
" Livedown 단축키"
nmap gm :LivedownToggle<CR>
```

npm 모듈 설치

```bash
npm install -g livedown
```

## 사용법

`:LivedownToggle`로 Preview를 시작 종료할 수 있음

이후 매 저장시마다 Preview가 업데이트됨.
