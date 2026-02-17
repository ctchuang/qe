# qe - Quick Emacs Client

## Purpose

1. Launch Emacs quicker

   - How?
     - Start Emacs server first (if not already running), then launch `emacsclient`

2. Launch different Emacs servers in different sessions

   - Ex: different servers for different `tmux` sessions and GUI mode.
   - Why?
      - We can work on one git repository in one `tmux` session by
        isolating opened buffers and tags tables ( `visit-tags-table`).

## Usage

Use the `qe` command to replace `emacs -nw`:

```sh
$ qe
$ qe [files-to-edit]
```

Use the `qe` command to replace `emacs` (GUI mode):
```sh
$ qew
$ qew [files-to-edit]
``` 

If you want to kill the background server process to save memory, 
`M-x kill-emacs` to kill all the clients and the server in the current
session. But the startup latency will increase next time when you run `qe`/`qew`.

## Misc

### Troubleshootings

- `C-h v server-name` (the currrent Emacs server/socket name)

###  Better UI in `tmux` 

If you want to run multiple `qe` in `tmux` to look like a tab-like UI,
add this to `.emacs`:

> TODO: add screenshot

```lisp
(defun inside-tmux-p ()
  "Return non-nil if Emacs is running inside a tmux session."
  (getenv "TMUX"))

(defun my-truncate-string (string max-length)
  (if (<= (length string) max-length)
      string
    (concat (substring string 0 max-length) "..")))

(defun update-tmux-title ()
  "Update tmux window name to the current Emacs buffer's name."
  (when (inside-tmux-p)
    (let ((filename (or (buffer-file-name) (buffer-name))))
      (call-process-shell-command (concat "tmux rename-window " "'e:" (my-truncate-string (file-name-nondirectory filename) 15) "'")))))

(add-hook 'window-configuration-change-hook 'update-tmux-title)
```

###  Trivia on Mac Setup

1. Recommended to use [Homebrew](https://brew.sh) to install Emacs with both GUI and terminal support:

```sh
  $ brew install --cask emacs
```

2.  Homebrew may register Emacs server in the background  with `LaunchServices` of Mac OS:

```sh
  # Check
  $ brew services list

  # Stop it if you want
  $ brew services stop emacs
```
