# i3bifocals

`i3bifocals` is a small wrapper around `i3status` that adds a block
containing the name of the currently focused window. It uses
asynchronous IPC to be informed by `i3` of focus or title changes, and
keeps the last status line emitted by `i3status` in memory, so it has
zero lag without ever forking a command to query the X server or needing
`i3status` to update more frequently.

# Usage

    i3bifocals [STATUS_COMMAND_TO_WRAP]

If no command to wrap is given, `i3status` will be used. I have not
tried using alternative implementations but anything that outputs JSON
status lines according to the spec (with the limitation that it must
output one array per line) should work.

# Wishlist

I would really love it if `i3bar` had a way to make a block use all
remaining available space (`max_width` cannot do this), because I could
then align the focused-title block left. I'll look into implementing
that at some point.
