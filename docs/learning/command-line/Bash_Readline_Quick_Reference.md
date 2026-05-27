---
description: Bash readline quick reference - keyboard shortcuts for command-line navigation, editing, history, and efficient terminal workflows.
---

# Bash Readline Quick Reference

## Overview

**Readline** is the library used by Bash (and many other programs) for command-line editing. It provides powerful keyboard shortcuts for navigation, editing, and history management.

**Why Master Readline:**
- Works in Bash, Python REPL, MySQL CLI, etc.
- Faster than using arrow keys
- Essential for SSH sessions with laggy connections
- Reduces hand movement (stay on home row)
- Works consistently across Unix/Linux systems

**Check Readline Bindings:**
```bash
bind -p | grep -v "^#" | grep -v "not bound"
```

---

## 1. Cursor Movement

### Basic Navigation

```
Ctrl+a - Move to beginning of line (A = start of alphabet)
Ctrl+e - Move to end of line (E = end)
Ctrl+b - Move back one character (B = back)
Ctrl+f - Move forward one character (F = forward)

Alt+b - Move back one word
Alt+f - Move forward one word
```

**Example:**
```bash
$ ls -la /var/log/application.log
^ cursor here

Ctrl+a → $ ls -la /var/log/application.log
^ cursor at start

Ctrl+e → $ ls -la /var/log/application.log
^ cursor at end

Alt+b → $ ls -la /var/log/application.log
^ cursor moved back one word
```

**Mnemonic:**
- **Ctrl** = character movement
- **Alt** (or **Esc**) = word movement

---

## 2. Editing Commands

### Delete/Kill

```
Ctrl+d - Delete character under cursor (D = delete)
Ctrl+h - Delete character before cursor (same as Backspace)

Ctrl+k - Kill (cut) from cursor to end of line (K = kill)
Ctrl+u - Kill from cursor to beginning of line
Ctrl+w - Kill word before cursor (W = word)

Alt+d - Kill word after cursor
Alt+Backspace - Kill word before cursor (alternative to Ctrl+w)
```

**Examples:**

```bash
# Delete from cursor to end of line
$ echo "This is a long command with many arguments"
^ cursor here
Ctrl+k → $ echo "
(text after cursor is killed/cut)

# Delete from cursor to start of line
$ git commit -m "Initial commit"
^ cursor here
Ctrl+u → $ "Initial commit"
(text before cursor is killed)

# Delete previous word
$ kubectl get pods --namespace production
^ cursor here
Ctrl+w → $ kubectl get pods --namespace
(word "production" is killed)
```

### Paste (Yank)

```
Ctrl+y - Yank (paste) last killed text
Alt+y - Rotate through kill ring (after Ctrl+y)
```

**Example:**
```bash
$ echo "delete this" keep this
^ select "delete this"
Ctrl+u → $ keep this
$ echo "paste here: "
Ctrl+y → $ echo "paste here: delete this"
```

### Transpose

```
Ctrl+t - Transpose (swap) characters (T = transpose)
Alt+t - Transpose words
```

**Example:**
```bash
# Fix typo "teh" → "the"
$ teh quick brown
^ cursor between e and h
Ctrl+t → $ the quick brown
(h and e swapped)

# Swap words
$ brown quick fox
^ cursor on "quick"
Alt+t → $ quick brown fox
```

### Case Manipulation

```
Alt+u - Uppercase word from cursor (U = uppercase)
Alt+l - Lowercase word from cursor (L = lowercase)
Alt+c - Capitalize word from cursor (C = capitalize)
```

**Example:**
```bash
$ echo hello world
^ cursor here
Alt+u → $ echo HELLO world

$ echo HELLO WORLD
^ cursor here
Alt+l → $ echo hello WORLD

$ echo hello world
^ cursor here
Alt+c → $ echo Hello world
```

---

## 3. Command History

### Basic History Navigation

```
Ctrl+p - Previous command in history (P = previous, same as Up arrow)
Ctrl+n - Next command in history (N = next, same as Down arrow)
```

### History Search

```
Ctrl+r - Reverse incremental search (R = reverse)
Ctrl+s - Forward incremental search (S = search)
(Note: Ctrl+s may be disabled by flow control, see troubleshooting)

Ctrl+g - Escape from history search mode
Ctrl+j or Enter - Execute found command
```

**Example:**
```bash
# Search command history
Ctrl+r
(reverse-i-search)`': 

# Type "git"
(reverse-i-search)`git': git status

# Press Ctrl+r again to find next match
(reverse-i-search)`git': git commit -m "Fix bug"

# Press Enter to execute or Ctrl+g to cancel
```

### History Expansion

```
!! - Repeat last command
!$ - Last argument of previous command
!^ - First argument of previous command
!* - All arguments of previous command
!n - Execute command number n from history
!-n - Execute command n lines back

Alt+. - Insert last argument of previous command
Alt+_ - Same as Alt+.
```

**Examples:**

```bash
$ ls /var/log/application.log
$ vim !$
# Expands to: vim /var/log/application.log

$ echo arg1 arg2 arg3
$ echo !^
# Expands to: echo arg1

$ sudo !!
# Repeat last command with sudo

$ cat /etc/hosts
$ vim Alt+.
# Inserts: vim /etc/hosts
```

### History Commands

```
history - Show command history
history 20 - Show last 20 commands
history -c - Clear history
!123 - Execute command 123 from history
!ssh - Execute last command starting with "ssh"
!?pattern - Execute last command containing "pattern"
```

---

## 4. Completion

### Tab Completion

```
Tab - Complete command/filename
Tab Tab - Show all possible completions

Alt+? - List possible completions (same as Tab Tab)
Alt+* - Insert all possible completions
```

**Examples:**

```bash
$ cd /var/lo[Tab]
$ cd /var/log/

$ systemctl sta[Tab][Tab]
start status stop

$ git comm[Tab]
$ git commit

$ ls *.tx[Tab]
$ ls *.txt
```

### Command-Specific Completion

Bash supports programmable completion for many commands:

```bash
# Git branch completion
$ git checkout ma[Tab]
$ git checkout main

# Kubernetes completion
$ kubectl get po[Tab]
$ kubectl get pods

# SSH hostname completion (from ~/.ssh/config and ~/.ssh/known_hosts)
$ ssh serv[Tab]
$ ssh server01
```

**Enable kubectl completion:**
```bash
source <(kubectl completion bash)
```

---

## 5. Advanced Editing

### Undo and Redo

```
Ctrl+_ or Ctrl+x Ctrl+u - Undo last edit
Alt+r - Revert line (undo all changes)
```

### Clear Screen and Line

```
Ctrl+l - Clear screen (L = clear, same as `clear` command)
Ctrl+c - Cancel current command / interrupt
Ctrl+d - Exit shell (if line is empty) / EOF (D = done)
Ctrl+z - Suspend current process (put in background)
```

### Quoted Insert

```
Ctrl+v - Insert next character literally (V = verbatim)
Ctrl+q - Same as Ctrl+v (Q = quote)
```

**Example:**
```bash
# Insert a Tab character literally (instead of completion)
$ echo "hello[Ctrl+v][Tab]world"
$ echo "hello	world"
^ actual tab character

# Insert Ctrl+C literally
$ echo "Press [Ctrl+v][Ctrl+c] to cancel"
$ echo "Press ^C to cancel"
```

---

## 6. Command Line Editing Modes

Readline supports two editing modes: **Emacs** (default) and **Vi**.

### Check Current Mode

```bash
set -o | grep -E 'emacs|vi'
```

### Switch Modes

```bash
# Switch to Vi mode
set -o vi

# Switch back to Emacs mode (default)
set -o emacs
```

### Vi Mode

In Vi mode, you start in **Insert mode** and can press `Esc` to enter **Normal mode**.

**Normal Mode Commands:**
```
h/j/k/l - Left/Down/Up/Right (like Vim)
w/b - Forward/Backward word
0/$ - Start/End of line
x - Delete character
dw - Delete word
dd - Delete line
yy - Yank line
p - Paste
u - Undo
/ - Search history forward
? - Search history backward
n - Next search result
```

**Example:**
```bash
$ git commit -m "Initial commit"
Esc → Enter normal mode
0 → Move to start of line
w → Move forward one word
dw → Delete "commit"
i → Back to insert mode
push → Type "push"
$ git push -m "Initial commit"
```

**Note:** Most SRE/DevOps professionals use Emacs mode (default) for consistency across systems.

---

## 7. Macros and Custom Bindings

### View Current Bindings

```bash
# List all key bindings
bind -p

# Search for specific binding
bind -p | grep '\\C-a'

# List function names
bind -l
```

### Create Custom Bindings

Add to `~/.inputrc`:

```bash
# Create ~/.inputrc if it doesn't exist
touch ~/.inputrc
```

**Example bindings:**

```bash
# Use up/down arrows for history search (like fish shell)
"\e[A": history-search-backward
"\e[B": history-search-forward

# Alt+l to lowercase current word
"\el": downcase-word

# Ctrl+x Ctrl+e to edit command in $EDITOR
"\C-x\C-e": edit-and-execute-command

# Show all completions without double-tab
set show-all-if-ambiguous on

# Ignore case in completions
set completion-ignore-case on

# Show completion list immediately
set show-all-if-unmodified on

# Color completion by file type
set colored-stats on

# Mark symlinked directories
set mark-symlinked-directories on
```

**Reload inputrc:**
```bash
bind -f ~/.inputrc
# or restart shell
```

### Useful Custom Bindings

```bash
# Ctrl+x Ctrl+e: Edit command in $EDITOR (Vim/Nano)
# Useful for complex multi-line commands
"\C-x\C-e": edit-and-execute-command
```

**Example:**
```bash
$ for i in {1..10}; do echo $i; done[Ctrl+x Ctrl+e]
# Opens command in Vim, edit, save (:wq), executes
```

---

## 8. Practical SRE/DevOps Workflows

### Quick Directory Navigation

```bash
# Jump to previous directory
$ cd -

# Jump to home directory
$ cd ~

# Jump to directory in $CDPATH
export CDPATH=.:~:/var/log:/etc
$ cd nginx # Jumps to /etc/nginx if it exists
```

**Readline shortcuts:**
```bash
$ cd /var/log/application/backend/service/logs
Ctrl+w Ctrl+w Ctrl+w → $ cd /var/log/application
```

### Efficient Command Building

```bash
# Reuse last argument
$ ls /var/log/app.log
$ vim [Alt+.]
# Becomes: vim /var/log/app.log

# Build command incrementally
$ kubectl get pods -n production
Ctrl+a → move to start
$ watch [Space]
# Becomes: watch kubectl get pods -n production
```

### Fix Typos Quickly

```bash
$ git statu
Ctrl+a → Move to start
Alt+f → Move to "statu"
$ git status

# Or use history substitution
$ ^statu^status^
# Runs: git status
```

### Search and Execute

```bash
# Find that long kubectl command from history
Ctrl+r
(reverse-i-search)`kubectl apply': kubectl apply -f deployment.yaml

# Edit before executing
Ctrl+a → Move to start
Alt+f → Move forward to filename
Ctrl+k → Kill to end
new-deployment.yaml → Type new filename
# Becomes: kubectl apply -f new-deployment.yaml
```

### Kill Ring Rotation

```bash
$ echo "first text"
Ctrl+u → Kill line
$ echo "second text"
Ctrl+u → Kill line
$ echo "third text"
Ctrl+u → Kill line

$ echo ""
Ctrl+y → Pastes: "third text"
Alt+y → Rotates to: "second text"
Alt+y → Rotates to: "first text"
```

---

## 9. History Management

### History Configuration

Add to `~/.bashrc`:

```bash
# Increase history size
export HISTSIZE=10000 # In-memory history
export HISTFILESIZE=20000 # On-disk history

# Avoid duplicates
export HISTCONTROL=ignoredups:erasedups

# Append to history, don't overwrite
shopt -s histappend

# Save multi-line commands as one entry
shopt -s cmdhist

# Timestamp in history
export HISTTIMEFORMAT="%F %T "

# Ignore common commands
export HISTIGNORE="ls:ll:cd:pwd:bg:fg:history"

# Save history after each command
PROMPT_COMMAND="history -a; history -c; history -r; $PROMPT_COMMAND"
```

### History Search Patterns

```bash
# Search for all git commands
$ history | grep git

# Execute specific history item
$ !1234

# Execute last kubectl command
$ !kubectl

# Search and confirm before executing
$ !git:p # Prints command, doesn't execute
$ !! # Then execute if correct
```

---

## 10. Troubleshooting

### Ctrl+S Freezes Terminal (Flow Control)

**Problem:** Pressing `Ctrl+s` freezes the terminal.

**Cause:** `Ctrl+s` activates XON/XOFF flow control.

**Solution:**
```bash
# Disable flow control (add to ~/.bashrc)
stty -ixon

# If frozen, press Ctrl+q to unfreeze
```

### Backspace Produces ^? or ^H

**Problem:** Backspace key doesn't work correctly.

**Solution:**
```bash
# Add to ~/.inputrc
"\C-h": backward-delete-char
"\C-?": backward-delete-char
```

### Alt Key Not Working (Sends Escape Sequences)

**Problem:** `Alt+b`, `Alt+f` don't work.

**Solution:**
```bash
# Terminal emulator settings (check):
# - "Use Option as Meta key" (iTerm2/Terminal.app on Mac)
# - "Alt sends Escape" (PuTTY on Windows)

# Or use Esc key instead of Alt
# Press Esc, then b (two separate keystrokes)
```

### Tab Completion Not Working

**Problem:** Tab completion missing or incomplete.

**Solution:**
```bash
# Install bash-completion (Fedora/RHEL)
sudo dnf install bash-completion

# Enable in ~/.bashrc
if [ -f /etc/bash_completion ]; then
. /etc/bash_completion
fi
```

### History Not Persisting

**Problem:** Command history is lost between sessions.

**Solution:**
```bash
# Check HISTFILE
echo $HISTFILE
# Should be: /home/username/.bash_history

# Ensure it's writable
ls -la ~/.bash_history

# Force write history
history -a
```

---

## 11. Readline in Other Programs

Readline shortcuts work in many CLI programs:

### Python REPL

```bash
$ python3
>>> import sys
>>> sys.^A^E^K
```

### MySQL CLI

```bash
mysql> SELECT * FROM users WHERE username = 'admin';
^A^E^B^B^B^B^K
```

### PostgreSQL (psql)

```bash
psql=# \d users
^P (previous command)
```

### Redis CLI

```bash
127.0.0.1:6379> GET mykey
^A^F^F^F (move forward)
```

### Node.js REPL

```bash
> console.log("Hello");
^U (clear line)
```

---

## 12. Cheat Sheet Summary

### Navigation
```
Ctrl+a - Beginning of line
Ctrl+e - End of line
Ctrl+b - Back one character
Ctrl+f - Forward one character
Alt+b - Back one word
Alt+f - Forward one word
```

### Editing
```
Ctrl+d - Delete character under cursor
Ctrl+h - Delete character before cursor
Ctrl+k - Kill to end of line
Ctrl+u - Kill to beginning of line
Ctrl+w - Kill previous word
Alt+d - Kill next word
Ctrl+y - Yank (paste) killed text
Ctrl+t - Transpose characters
Alt+t - Transpose words
```

### History
```
Ctrl+p - Previous command
Ctrl+n - Next command
Ctrl+r - Reverse search
Ctrl+g - Cancel search
!! - Repeat last command
!$ - Last argument
Alt+. - Insert last argument
```

### Control
```
Ctrl+l - Clear screen
Ctrl+c - Cancel/Interrupt
Ctrl+d - Exit (if line empty)
Ctrl+z - Suspend process
Tab - Complete
Tab Tab - Show completions
```

---

## 13. Practice Exercises

**Exercise 1: Navigation Drills**
```bash
# Type this command, then practice navigation:
$ kubectl apply -f /path/to/kubernetes/manifests/deployment.yaml

# Practice:
# 1. Move to beginning (Ctrl+a)
# 2. Move forward 3 words (Alt+f x3)
# 3. Move to end (Ctrl+e)
# 4. Move back 2 words (Alt+b x2)
```

**Exercise 2: Quick Edits**
```bash
# Type:
$ docker run -it --name mycontainer ubuntu:latest

# Change "run" to "exec":
# Ctrl+a, Alt+f, Ctrl+w, type "exec"

# Change container name:
# Alt+f x3, Ctrl+k, type "newcontainer"
```

**Exercise 3: History Mastery**
```bash
# Execute several git commands:
$ git status
$ git add .
$ git commit -m "Fix"
$ git push origin main

# Now:
# 1. Search for "commit" (Ctrl+r, type "commit")
# 2. Change message (Ctrl+e, Ctrl+w, Ctrl+w, type new message)
# 3. Execute
```

---

## 14. Configuration Template

**~/.inputrc** (recommended settings):

```bash
# Use vi or emacs mode (choose one)
set editing-mode emacs
# set editing-mode vi

# Show completion immediately
set show-all-if-ambiguous on
set show-all-if-unmodified on

# Case-insensitive completion
set completion-ignore-case on

# Color completion by type
set colored-stats on
set colored-completion-prefix on

# Show symlink directories with /
set mark-symlinked-directories on

# Show file type indicators
set visible-stats on

# Single tab for completion
set show-all-if-ambiguous on

# Pager for long completion lists
set page-completions on

# Up/Down arrow history search
"\e[A": history-search-backward
"\e[B": history-search-forward

# Ctrl+arrows for word movement
"\e[1;5C": forward-word
"\e[1;5D": backward-word

# Edit command in editor
"\C-x\C-e": edit-and-execute-command
```

**~/.bashrc** (recommended settings):

```bash
# History size
export HISTSIZE=10000
export HISTFILESIZE=20000

# History control
export HISTCONTROL=ignoredups:erasedups
shopt -s histappend
shopt -s cmdhist

# Timestamp
export HISTTIMEFORMAT="%F %T "

# Ignore common commands
export HISTIGNORE="ls:ll:cd:pwd:bg:fg:history:clear"

# Save after each command
PROMPT_COMMAND="history -a; $PROMPT_COMMAND"

# Enable bash completion
if [ -f /etc/bash_completion ]; then
. /etc/bash_completion
fi

# Disable flow control (enable Ctrl+s search)
stty -ixon
```

---

## References

**Official Documentation:**
- Readline Manual: https://tiswww.case.edu/php/chet/readline/rltop.html
- Bash Manual: https://www.gnu.org/software/bash/manual/
- `man readline`
- `man bash`

**Cheat Sheets:**
- https://readline.kablamo.org/emacs.html
- https://www.bigsmoke.us/readline/shortcuts

**Interactive Practice:**
- https://www.shortcutfoo.com/app/dojos/command-line

---

**Updated:** 2026-05-23 
**Author:** Documentation Team 
**Use Case:** Command-line efficiency, SRE, DevOps, System Administration
