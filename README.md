# i3-auto-switch

Auto switch between multiple neovim/vim instance or terminal instance in i3.

Sometimes, you forget in which workspace or which tab you put your vim/neovim in? 
This module will focus your vim/neovim for you, and allows you to switch between all your currently opened vim/neovim instances with one keyboard shortcut.

## How to use

Type your `mod+o`, it will switch between all your opened vim instance in all workspaces.
Of course, it's prefered to only have one vim instance, but we all know that it's not always the case...

## Install

```shell
cat <<\EOF | sudo tee /usr/bin/i3-auto-switch
# the file name that will be used to store the currently focused vim id
windows_title="$1"
export DISPLAY=":0"
CURRENT=$(xdotool getactivewindow)
# find the next vim windows id, the one next to the id stored in /tmp/previous_vim
NEXT=$(xdotool search --all --name "$windows_title" \
  | sort -n \
  | grep -A 1 "$CURRENT" \
  | grep -v "$CURRENT")

# if NEXT is empty, use the first matching windows id
NEXT=$((echo $NEXT ; (xdotool search --all --name "$windows_title" | sort -n)) | grep -v '^$' | head -n1)

# focus the found windows id
i3-msg [id="$NEXT"] focus
EOF

sudo chmod +x /usr/bin/i3-auto-switch
```

## Add the keyboard shortcut to your ~/.config/i3/config

```i3
bindsym $mod+o exec "DISPLAY=:0 bash -c '/usr/bin/i3-auto-switch vim'"
```

## Techical details

- i3-auto-switch uses xdotool to find the ID of the currently focused vim.
- i3-auto-switch uses xdotool to find the ID of all currently opened vim.
- i3-auto-switch uses a combinaison grep/sort/head to define what is the next vim to be focused.

Enjoy!

---

Powered By: [chaignc+cve-2017-5123@hexpresso.team](https://twitter.com/chaignc)
