在`~/.vimrc`中配置
```shell
" ======================= 基本操作 =======================
set nocp                        " 不兼容vi
syntax on                       " 关键字上色
syntax enable                   " 语法高亮
set nu                          " 显示行号
set hidden                      " 允许不保存切换buffer
set splitright                  " 新分割窗口在右边
set splitbelow                  " 新分割窗口在下边
set autoread                    " 文件在Vim之外修改过，自动重新读入
set timeoutlen=350              " 等待时间,如<leader>键后的输入
set helpheight=999              " 查看帮助文档全屏
set scrolljump=3                " 当光标离开屏幕滑动行数
set scrolloff=1                 " 保持在光标上下最少行数
set showmatch                   " 短暂回显匹配括号
set hlsearch                    " 检索时高亮显示匹配项
set incsearch                   " 边输入边搜索
set ignorecase                  " 搜索忽略大小写
set smartcase                   " 智能大小写搜索
set wildmenu                    " 命令模式下补全以菜单形式显示
set wildmode=list:longest,full  " 命令模式补全模式
set foldenable                  " 启动折叠
set foldmethod=marker           " 设置折叠模式
set encoding=utf-8              " 编码，使汉语正常显示
set termencoding=utf-8
set fileencodings=utf-8,gb2312,gbk,gb18030


" ======================= 缩进操作 =======================
set expandtab                   " tab=空格
set tabstop=4                   " tab缩进4个空格
set shiftwidth=4                " 自动缩进空格数
set softtabstop=4               " 退格删除缩进
set backspace=indent,start      " 退格可删除缩进和原有字符
set autoindent                  " 与前一行同样等级缩进

" ======================= 快捷键 =======================
autocmd BufWritePost $MYVIMRC source $MYVIMRC " 让配置变更立即生效
let mapleader=";"								" 定义快捷键的前缀，即<Leader>

 " ======================= 插件管理 =======================
 filetype off
 set rtp+=~/.vim/bundle/Vundle.vim
 " vundle 管理的插件列表必须位于 vundle#begin() 和 vundle#end() 之间
 call vundle#begin()
 Plugin 'VundleVim/Vundle.vim'
 Plugin 'tomasr/molokai'
 
 call vundle#end()
 filetype plugin indent on

" ======================= Vim UI =======================
set t_Co=256                    " 终端显示256色
set tabpagemax=15               " 最多15个Tab
set showmode                    " 显示当前mode
set cursorline                  " 高亮当前行
" set cursorcolumn                " 高亮当前列
highlight cursorLine   cterm=NONE ctermbg=black ctermfg=none guibg=NONE guifg=NONE
" highlight cursorColumn cterm=NONE ctermbg=black ctermfg=none guibg=NONE guifg=NONE
set list                        " 显示特殊符号
set listchars=tab:›\ ,trail:•,extends:#,nbsp:.

hi clear SignColumn             " 标记列背景和主题背景匹配
hi clear LineNr                 " 当前行列背景和主题背景匹配

hi CursorLineNr ctermfg=red
hi VertSplit ctermbg=Grey ctermfg=Grey cterm=none
hi Visual ctermbg=81 ctermfg=black cterm=none
hi Comment ctermfg=blue
hi Statement ctermfg=cyan
hi DiffAdd ctermbg=blue ctermfg=white
hi DiffDelete ctermbg=green ctermfg=none
hi DiffChange ctermbg=red ctermfg=White
hi DiffText ctermbg=yellow ctermfg=black

if has('cmdline_info')
    set showcmd                 " 右下角显示当前操作
    set ruler                   " 右下角显示状态说明
    set rulerformat=%30(%=\:b%n%y%m%r%w\ %l,%c%V\ %P%) " 设定格式
endif

if has('statusline')
    set laststatus=1
    set statusline=%<%f\                     " Filename
    set statusline+=%w%h%m%r                 " Options
    set statusline+=\ [%{&ff}/%Y]            " Filetype
    set statusline+=\ [%{getcwd()}]          " Current dir
    set statusline+=%=%-14.(%l,%c%V%)\ %p%%  " Right aligned file nav info
endif
```