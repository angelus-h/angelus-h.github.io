---
description: Vim quick reference - modal editing, navigation, text manipulation, commands, visual mode, search, replace, and configuration essentials.
---

# Vim Quick Reference

## Overview

**Vim** (Vi IMproved) is a highly configurable text editor built for efficient text editing. It's modal (Normal, Insert, Visual, Command modes) and ubiquitous on Unix/Linux systems.

**Why Vim:**
- Available on every Linux/Unix system
- Extremely fast once mastered
- Powerful text manipulation
- Works over SSH (no GUI needed)
- Highly extensible (plugins, vimrc)

**Check Version:**
```bash
vim --version
```

---

## 1. Vim Modes

Vim has several modes. Understanding them is critical:

| Mode | Purpose | Enter From Normal Mode | Exit to Normal Mode |
|------|---------|------------------------|---------------------|
| **Normal** | Navigation, commands | N/A (default) | N/A |
| **Insert** | Type text | `i`, `a`, `o`, `I`, `A`, `O` | `Esc` or `Ctrl+[` |
| **Visual** | Select text | `v`, `V`, `Ctrl+v` | `Esc` |
| **Command** | Run commands | `:` | `Enter` or `Esc` |
| **Replace** | Overwrite text | `R` | `Esc` |

**Key Principle:** Always return to Normal mode after editing. Press `Esc` liberally.

---

## 2. Basic Navigation (Normal Mode)

### Character Movement

```
h - Move left
j - Move down
k - Move up
l - Move right
```

**Mnemonic:** `h` (left), `l` (right), `j` (down looks like down arrow), `k` (up)

### Word Movement

```
w - Move forward to start of next word
b - Move backward to start of previous word
e - Move to end of current/next word
ge - Move to end of previous word

W, B, E - Same as above, but treat punctuation as part of words
```

**Examples:**
```
Cursor on: "hello-world.example"

w stops at: h w . e
 hello-world.example
 ^ ^ ^ ^ ^

W stops at: h e
 hello-world.example
 ^ ^
```

### Line Movement

```
0 - Move to start of line (column 0)
^ - Move to first non-blank character
$ - Move to end of line
g_ - Move to last non-blank character
```

### Screen Movement

```
H - Move to top of screen (High)
M - Move to middle of screen (Middle)
L - Move to bottom of screen (Low)

Ctrl+u - Scroll up half page
Ctrl+d - Scroll down half page
Ctrl+b - Scroll up full page (Back)
Ctrl+f - Scroll down full page (Forward)

gg - Go to first line of file
G - Go to last line of file
:42 or 42G - Go to line 42

zz - Center current line on screen
zt - Move current line to top
zb - Move current line to bottom
```

### Search Movement

```
f<char> - Jump forward to next <char> on current line
F<char> - Jump backward to previous <char> on current line
t<char> - Jump forward to just before <char>
T<char> - Jump backward to just after <char>

; - Repeat last f/F/t/T forward
, - Repeat last f/F/t/T backward
```

**Example:**
```
Line: "const API_KEY = 'abc123';"

Cursor at start (c):
f= → jumps to =
fa → jumps to first a (in API)
t; → jumps to character before ;
```

---

## 3. Editing Commands (Normal Mode)

### Insert Mode Entries

```
i - Insert before cursor
a - Insert after cursor (Append)
I - Insert at start of line
A - Insert at end of line

o - Open new line below and insert
O - Open new line above and insert

s - Delete character and insert (Substitute)
S - Delete line and insert
```

### Delete Commands

```
x - Delete character under cursor
X - Delete character before cursor

dw - Delete from cursor to start of next word
db - Delete from cursor to start of previous word
de - Delete from cursor to end of word

dd - Delete entire line
D - Delete from cursor to end of line
d$ - Same as D
d0 - Delete from cursor to start of line

3dd - Delete 3 lines
d3w - Delete 3 words
```

### Change Commands

```
cw - Change word (delete word and enter insert mode)
cb - Change backward word
ce - Change to end of word

cc - Change entire line
C - Change from cursor to end of line

ciw - Change inner word (works anywhere in word)
ci" - Change inside quotes
ci( - Change inside parentheses
ci{ - Change inside braces
cit - Change inside HTML/XML tag

3cw - Change 3 words
```

**Example:**
```
Original: const API_KEY = "old_value";
Cursor on: old_value

ci" → deletes old_value, enters insert mode inside quotes
Type: new_value
Result: const API_KEY = "new_value";
```

### Copy (Yank) and Paste

```
yy or Y - Yank (copy) entire line
yw - Yank word
yiw - Yank inner word
y$ - Yank to end of line
y0 - Yank to start of line

3yy - Yank 3 lines
y3w - Yank 3 words

p - Paste after cursor/below line
P - Paste before cursor/above line

"ayy - Yank line into register a
"ap - Paste from register a
```

### Undo/Redo

```
u - Undo last change
Ctrl+r - Redo (undo the undo)

3u - Undo last 3 changes
```

---

## 4. Visual Mode Selection

### Enter Visual Mode

```
v - Character-wise visual mode
V - Line-wise visual mode
Ctrl+v - Block visual mode (column selection)
```

### Visual Mode Operations

Once in visual mode, move cursor to select, then:

```
d or x - Delete selection
y - Yank (copy) selection
c - Change selection (delete and enter insert mode)
> - Indent selection right
< - Indent selection left
~ - Toggle case
u - Convert to lowercase
U - Convert to uppercase
```

**Examples:**

**1. Delete multiple lines:**
```
V → Enter line visual mode
3j → Select 3 lines down
d → Delete
```

**2. Block edit (add # to multiple lines):**
```
Ctrl+v → Enter block visual mode
5j → Select 5 lines down
I → Enter insert mode
# → Type #
Esc → Apply to all selected lines
```

**3. Comment out code block:**
```
V → Line visual mode
} → Select until next paragraph/block
:s/^/# / → Add "# " at start of each line
```

---

## 5. Search and Replace

### Search

```
/pattern - Search forward for pattern
?pattern - Search backward for pattern
n - Next match (forward)
N - Previous match (backward)

* - Search forward for word under cursor
# - Search backward for word under cursor

:noh or :nohlsearch - Clear search highlighting
```

**Example:**
```
/TODO → Search for "TODO"
n → Next TODO
N → Previous TODO
```

### Replace (Substitute)

```
:s/old/new/ - Replace first occurrence on current line
:s/old/new/g - Replace all occurrences on current line
:s/old/new/gc - Replace all with confirmation

:%s/old/new/g - Replace all in file
:%s/old/new/gc - Replace all in file with confirmation

:10,20s/old/new/g - Replace in lines 10-20
:'<,'>s/old/new/g - Replace in visual selection
```

**Flags:**
- `g` - Global (all occurrences on line)
- `c` - Confirm each replacement
- `i` - Case insensitive
- `I` - Case sensitive

**Examples:**

```
# Replace API key format
:%s/API_KEY = '.*'/API_KEY = 'new_key'/g

# Remove trailing whitespace
:%s/\s\+$//g

# Convert tabs to 4 spaces
:%s/\t/ /g

# Add semicolon to end of lines 10-50
:10,50s/$/;/g

# Case insensitive replace
:%s/error/warning/gi
```

---

## 6. Working with Files

### Open and Save

```
:e filename - Open (edit) file
:w - Save (write) file
:w filename - Save as filename
:wq or :x or ZZ - Save and quit
:q - Quit (fails if unsaved)
:q! - Quit without saving
:qa - Quit all windows
:wqa - Save and quit all
```

### File Exploration

```
:e . - Open file explorer in current directory
:Ex - Open explorer
:Sex - Open explorer in horizontal split
:Vex - Open explorer in vertical split

In explorer:
Enter - Open file/directory
- - Go up to parent directory
d - Create directory
% - Create new file
R - Rename file
D - Delete file
```

### Multiple Files

```
:e file2.txt - Switch to file2.txt
:bn - Next buffer
:bp - Previous buffer
:ls or :buffers - List all buffers
:b <number> - Switch to buffer number
:b filename - Switch to buffer by filename
:bd - Delete (close) current buffer
```

---

## 7. Windows and Tabs

### Split Windows

```
:split or :sp - Horizontal split
:vsplit or :vsp - Vertical split
:sp filename - Open filename in horizontal split
:vsp filename - Open filename in vertical split

Ctrl+w s - Horizontal split (same file)
Ctrl+w v - Vertical split (same file)

Ctrl+w h/j/k/l - Navigate between windows
Ctrl+w w - Cycle through windows
Ctrl+w c - Close current window
Ctrl+w o - Close all other windows (only current remains)

Ctrl+w = - Make all windows equal size
Ctrl+w _ - Maximize current window height
Ctrl+w | - Maximize current window width
```

### Tabs

```
:tabnew or :tabnew file - Open new tab
:tabn or gt - Next tab
:tabp or gT - Previous tab
:tabclose - Close current tab
:tabonly - Close all other tabs
:tabs - List all tabs
```

---

## 8. Advanced Editing

### Marks and Jumps

```
ma - Set mark 'a' at current position
'a - Jump to mark 'a'
`a - Jump to exact position of mark 'a'

:marks - List all marks

Ctrl+o - Jump to previous location
Ctrl+i - Jump to next location
`` - Jump to position before last jump
'. - Jump to last edit
```

### Macros

```
qa - Start recording macro into register 'a'
<commands>
q - Stop recording

@a - Execute macro 'a'
@@ - Repeat last macro
5@a - Execute macro 'a' 5 times
```

**Example: Add quotes around words on multiple lines**
```
qa → Start recording macro 'a'
I" → Insert " at start of line
Esc → Back to normal mode
A" → Append " at end of line
Esc → Back to normal mode
j → Move down one line
q → Stop recording

10@a → Execute macro on next 10 lines
```

### Text Objects

Text objects are used with commands like `d`, `c`, `y`:

```
iw - Inner word
aw - A word (includes surrounding whitespace)
is - Inner sentence
as - A sentence
ip - Inner paragraph
ap - A paragraph

i" or i' - Inside quotes
a" or a' - Around quotes (includes quotes)
i( or i) or ib - Inside parentheses
a( or a) or ab - Around parentheses
i{ or i} or iB - Inside braces
a{ or a} or aB - Around braces
i[ or i] - Inside brackets
a[ or a] - Around brackets
it - Inside HTML/XML tag
at - Around HTML/XML tag
```

**Examples:**
```
daw - Delete a word (with surrounding space)
ci" - Change inside quotes
di( - Delete inside parentheses
yit - Yank inside HTML tag
dat - Delete around tag (tag and content)
```

### Indentation

```
>> - Indent line right
<< - Indent line left
3>> - Indent 3 lines right

== - Auto-indent current line
gg=G - Auto-indent entire file

In visual mode:
> - Indent selection right
< - Indent selection left
= - Auto-indent selection
```

### Join Lines

```
J - Join current line with next (removes newline)
3J - Join 3 lines
gJ - Join without adding space
```

### Sorting

```
:sort - Sort lines alphabetically
:sort! - Sort in reverse
:sort u - Sort and remove duplicates
:10,20sort - Sort lines 10-20
:'<,'>sort - Sort visual selection
```

---

## 9. Command Mode Power Features

### Execute Shell Commands

```
:!command - Execute shell command
:!ls -la - List directory
:!date - Show current date/time
:r !command - Read command output into file
:r !date - Insert current date

:.!command - Replace current line with command output
:%!command - Filter entire file through command
:'<,'>!command - Filter visual selection through command

:w !sudo tee % - Save file with sudo (when opened without sudo)
```

**Example:**
```
# Sort selected lines using external sort command
:'<,'>!sort

# Run Python script and insert output
:r !python script.py

# Format JSON with jq
:%!jq .
```

### Read and Write Partial Files

```
:r filename - Read filename and insert below cursor
:10r filename - Insert filename below line 10

:10,20w newfile - Write lines 10-20 to newfile
:'<,'>w selection.txt - Write visual selection to file
```

### Global Commands

```
:g/pattern/command - Execute command on lines matching pattern
:g!/pattern/command - Execute command on lines NOT matching pattern
:v/pattern/command - Same as :g! (inverse)

:g/TODO/d - Delete all lines containing "TODO"
:g/^$/d - Delete all empty lines
:g/^#/d - Delete all comment lines (starting with #)
:v/error/d - Delete all lines NOT containing "error"
```

---

## 10. Configuration and Customization

### Runtime Settings

```
:set number or :set nu - Show line numbers
:set nonumber or :set nonu - Hide line numbers
:set relativenumber or :set rnu - Relative line numbers

:set hlsearch - Highlight search matches
:set incsearch - Incremental search (search as you type)
:set ignorecase - Case-insensitive search
:set smartcase - Case-sensitive if search contains uppercase

:set tabstop=4 - Tab width (display)
:set shiftwidth=4 - Indent width (>>, <<)
:set expandtab - Use spaces instead of tabs
:set noexpandtab - Use tabs

:set autoindent - Auto-indent new lines
:set smartindent - Smart auto-indenting

:set paste - Paste mode (disables auto-indent)
:set nopaste - Exit paste mode

:syntax on - Enable syntax highlighting
:syntax off - Disable syntax highlighting

:set list - Show invisible characters
:set nolist - Hide invisible characters
```

### .vimrc Configuration

Create `~/.vimrc` for persistent settings:

```vim
" Basic Settings
set number " Show line numbers
set relativenumber " Relative line numbers
set tabstop=4 " Tab width
set shiftwidth=4 " Indent width
set expandtab " Use spaces instead of tabs
set autoindent " Auto-indent
set smartindent " Smart auto-indent
set hlsearch " Highlight search
set incsearch " Incremental search
set ignorecase " Case-insensitive search
set smartcase " Case-sensitive if uppercase present
syntax on " Syntax highlighting

" UI Enhancements
set cursorline " Highlight current line
set wildmenu " Enhanced command-line completion
set showmatch " Show matching brackets
set ruler " Show cursor position

" Performance
set lazyredraw " Don't redraw during macros

" Key Mappings
" Clear search highlight with Ctrl+L
nnoremap <C-L> :nohlsearch<CR><C-L>

" Save with Ctrl+S (add to .bashrc: stty -ixon)
nnoremap <C-S> :w<CR>

" Easier window navigation
nnoremap <C-J> <C-W><C-J>
nnoremap <C-K> <C-W><C-K>
nnoremap <C-L> <C-W><C-L>
nnoremap <C-H> <C-W><C-H>
```

---

## 11. Practical SRE/DevOps Examples

### Edit Configuration Files

```bash
# Edit Kubernetes YAML
vim deployment.yaml

# Jump to replicas field
/replicas
# Change value
cw → type new number → Esc

# Add annotation (in visual mode)
:149 → go to line 149
o → new line
annotations: → type
Ctrl+d → decrease indent

:wq → save and exit
```

### Log File Analysis

```bash
vim /var/log/application.log

# Search for errors
/ERROR
n → next error

# Delete all INFO logs
:g/INFO/d

# Keep only ERROR and WARN
:v/ERROR\|WARN/d

# Extract errors to new file
:g/ERROR/w errors.txt

# Count occurrences
:%s/ERROR//gn
```

### Quick Edits Over SSH

```bash
ssh server01 "vim -c ':%s/old_ip/new_ip/g' -c ':wq' /etc/config.conf"
```

### Diff Files

```bash
# Open vim in diff mode
vimdiff file1.txt file2.txt

# In vim
:diffsplit file2.txt

# Navigate differences
]c - Next difference
[c - Previous difference

# Merge changes
do - Obtain (get) change from other file
dp - Put change to other file

:diffupdate - Refresh diff
```

### Bulk Edit Multiple Files

```bash
# Add shebang to all Python files
vim *.py
:argdo 1s/^/#!/usr/bin/env python3\r/ | update
:qa

# Replace in all YAML files
vim *.yaml
:argdo %s/old/new/ge | update
:qa
```

### YAML/JSON Formatting

```vim
" In vim, format entire YAML file
gg=G

" Format JSON with jq (if installed)
:%!jq .

" Validate YAML with yamllint
:!yamllint %

" Validate JSON
:%!python -m json.tool
```

---

## 12. Vim with Git

### Git Integration (with vim-fugitive plugin)

```vim
:Git status or :Gstatus
:Git add % " Stage current file
:Git commit
:Git push
:Git blame " Show git blame
:Gdiff " Git diff in split
```

### Edit Commit Messages

```bash
git config --global core.editor vim

# When committing
git commit
# Vim opens:
# - Write message on first line
# - Esc → :wq to save and commit
```

### Interactive Rebase

```bash
git rebase -i HEAD~3
# Vim opens with commit list

# Commands:
# p, pick = use commit
# r, reword = use commit, edit message
# e, edit = use commit, stop for amending
# s, squash = meld into previous commit
# f, fixup = like squash, discard message
# d, drop = remove commit

# Edit, save (:wq), Git continues rebase
```

---

## 13. Useful Plugins

### Plugin Manager: vim-plug

Install vim-plug:
```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
 https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

Add to `~/.vimrc`:
```vim
call plug#begin('~/.vim/plugged')

" File explorer
Plug 'preservim/nerdtree'

" Fuzzy file finder
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'

" Git integration
Plug 'tpope/vim-fugitive'

" Status line
Plug 'vim-airline/vim-airline'

" Auto-pairs (brackets, quotes)
Plug 'jiangmiao/auto-pairs'

" Syntax checking
Plug 'dense-analysis/ale'

call plug#end()
```

Install plugins:
```vim
:PlugInstall
```

---

## 14. Troubleshooting and Recovery

### Swap Files

If you see "swap file exists" warning:

```
O - Open Read-Only
E - Edit anyway (dangerous if another process is using it)
R - Recover (restore from swap)
Q - Quit
D - Delete swap file
```

**Find and clean swap files:**
```bash
# List swap files
ls -la ~/.vim/swap/

# Remove all swap files (if no vim processes running)
rm ~/.vim/swap/*.swp
```

### Undo Recovery

```vim
:earlier 5m - Undo to state 5 minutes ago
:earlier 3h - Undo to state 3 hours ago
:later 2m - Redo to state 2 minutes later

:undolist - Show undo tree
```

### Persistent Undo

Enable in `~/.vimrc`:
```vim
set undofile
set undodir=~/.vim/undo
```

Create directory:
```bash
mkdir -p ~/.vim/undo
```

---

## 15. Cheat Sheet Summary

### Most Common Commands

```
Navigation:
 h/j/k/l - Left/Down/Up/Right
 w/b - Next/Previous word
 0/$ - Start/End of line
 gg/G - Start/End of file
 Ctrl+d/u - Half page down/up

Editing:
 i/a - Insert before/after cursor
 I/A - Insert start/end of line
 o/O - Open line below/above
 x - Delete character
 dd - Delete line
 yy - Yank (copy) line
 p/P - Paste after/before
 u - Undo
 Ctrl+r - Redo

Visual:
 v/V/Ctrl+v - Char/Line/Block visual
 d/y/c - Delete/Yank/Change

Search:
 /pattern - Search forward
 n/N - Next/Previous match
 */# - Search word under cursor

File:
 :w - Save
 :q - Quit
 :wq - Save and quit
 :q! - Quit without saving
 :e file - Open file

Replace:
 :%s/old/new/g - Replace all in file
 :%s/old/new/gc - Replace all with confirm
```

---

## References

**Official Documentation:**
- https://www.vim.org/docs.php
- `:help` in Vim
- `:help <command>` for specific help

**Interactive Tutorials:**
- `vimtutor` - Built-in tutorial (run in terminal)
- https://vim-adventures.com/ - Game-based learning
- https://www.openvim.com/ - Interactive tutorial

**Cheat Sheets:**
- https://vim.rtorr.com/
- https://devhints.io/vim

---

**Updated:** 2026-05-23 
**Author:** Documentation Team 
**Use Case:** Text editing, SRE, DevOps, Programming
