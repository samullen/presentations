# tmux

SamuelMullen.com

@samullen

!

# Audience Participation

!

# What is tmux?

* A Terminal Multiplexer

!

# Terminology

* Sessions

* Windows

* Panes

!

# Sessions

"A single collection of pseudo terminals under the management of tmux."

* You can have multiple sessions

* Once all sessions are destroyed, the tmux server exits.

* Common practice is to segregate projects into sessions 

!

# Sessions

## Creating, Attaching, and Detaching

!

# Sessions

## Creating

* tmux

* tmux new-session -s session_name

!

# Sessions

## Detaching

PREFIX + d

!

# Sessions

## Attaching

tmux list-sessions

tmux attach-session -t session_name

!

# Sesssions

## Examples

!

# Windows

* Windows are the entire screen within a session. 

* You can have multiple windows

* Windows can be divided into panes

!

# Windows

## Creating new Windows

PREFIX + c

!

# Windows

## Renaming Windows

PREFIX + ","

!

# Windows

## Switching between windows

* Next Window: PREFIX + n

* Preview Window: PREFIX + p

* A Specific Window: PREFIX + window id

!

# Windows

## Examples

!

# Panes

* Divide windows into sections

* Each pane is a pseudo terminal

!

# Panes

## Creation

* Vertical split: PREFIX + %

* Horizontal split: PREFIX + \"

Hint: Remap to | and - for extra mnemonic goodness

!

# Panes

## Movement

* PREFIX + arrow keys

* Remap to use Vim or Emacs movement keys

!

# Panes

## Examples

!

# Configuration

I won't bore you with the details. 

You can get started with mine if you like

* http://github.com/samullen/dotfiles

!

# tmux

## Why use it?

* Separation of concerns (i.e. project sessions)

* Environmental stability

* Remote pairing

* Increased development speed


