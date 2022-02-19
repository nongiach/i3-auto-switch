# i3-auto-switch

TLDR: Auto switch between multiple neovim/vim instance or terminal instance in i3.

Sometimes, you forget in which workspace or which tab you your vim/neovim is? 
This module will focus your vim/neovim for you, and allows you to switch between all your currently opened vim/neovim instances with one keyboard shortcut.

## How to use

Type your `mod+o`, it will switch between all your opened vim instance in all workspaces.
Of course, a vim best practice is to only have one instance, but we all know that it's not always the case...

## Install

Copy bash the below commands into your shell, that's it!

```shell
cat <<\EOF | sudo tee /usr/bin/i3-auto-switch
# the file name that will be used to store the currently focused vim id
windows_title="$1"
export DISPLAY=":0"
CURRENT=$(xdotool getactivewindow)
# find the next vim windows id, the one next to the id stored in /tmp/previous_vim
xdotool search --all --name "[^]]$windows_title" getwindowname %@
NEXT=$(xdotool search --all --name "[^]]$windows_title" \
  | sort -n \
  | grep -A 1 "$CURRENT" \
  | grep -v "$CURRENT")

# if NEXT is empty, use the first matching windows id
NEXT=$((echo $NEXT ; (xdotool search --all --name "[^]]$windows_title" | sort -n)) | grep -v '^$' | head -n1)

# focus the found windows id
echo i3-msg [id="$NEXT"] focus
i3-msg [id="$NEXT"] focus
EOF

sudo chmod +x /usr/bin/i3-auto-switch
```

## Add the keyboard shortcut to your ~/.config/i3/config

```i3
bindsym $mod+o exec "bash -c '/usr/bin/i3-auto-switch vim'"
```
### What if I don't use vim/neovim?

This actually works for any application: emacs, firefox, terminal...
Just update the application title pattern in the below i3 configuration.
This one is how I switch between all my terminal instance.
```i3
bindsym $mod+i exec "bash -c '/usr/bin/i3-auto-switch term'"
```

## Techical details

- i3-auto-switch uses xdotool to find the ID of the currently focused vim.
- i3-auto-switch uses xdotool to find the ID of all currently opened vim.
- i3-auto-switch uses a combinaison grep/sort/head to define what is the next vim to be focused.

Enjoy!

---

Powered By: [@chaignc](https://twitter.com/chaignc)
