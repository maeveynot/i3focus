# i3bifocals

`i3bifocals` is a small wrapper around `i3status` that adds asynchronous
IPC with i3. It uses this to be informed of changes in the currently
focused window and add its title to the status line. I find that the
increased visual feedback makes layouts with `pixel` or `none` borders
more effective.

`i3status` is very good at what it does; let's not reimplement it.
However, it is designed to be synchronous in order to reduce code
complexity and resource usage, and intended to be used with intervals
that are some integer factor of 60 seconds (the higher, the better).

For our purposes, even 1 second is a very long time; we need zero lag.
However, updating all status information more frequently than that would
be wasteful, and repeatedly polling the X server or forking a process
would be even worse. We need another layer that augments `i3status`
output, and handles instant updates over IPC while only receiving input
over a pipe every few seconds. Python's asyncio works very well for
doing this without having to resort to multiple threads, and the
`i3ipc` library supports it.

We keep the last line output by `i3status` in memory, and re-emit it
with a different focused-window block on focus change, or re-emit the
same focused window with all the other blocks updated when we get input
from the `i3status` process.

# Usage

To run (as your `status_command`):

    i3bifocals [STATUS_COMMAND_TO_WRAP]

If no command to wrap is given, `i3status` will be used. I have only
done a very cursory smoke test with `i3blocks` but anything that outputs
JSON status lines according to the
[protocol](https://i3wm.org/docs/i3bar-protocol.html) should work.

JSON is the only supported format. `i3bifocals`'s output will not be
parseable by scripts that require lines to start with a comma or not
contain any whitespace.

# Limitations

The focused-window block is added at the head of the status line. This
is unlikely to be made configurable, because its width can change
dramatically, and if it were anywhere else all the other blocks would
move around.

# Future Directions

Another thing we could do with async I/O is subscribe to changes on a
file with inotify(7). This would address the problem of users wanting
to run programs from within their status command (not, in my opinion,
a good idea): expensive processes could be scheduled independently of
i3, writing their output to a file. It could also be used for tailing
logs, I suppose, but this would take some care to do efficiently.

# Wishlist

I would really love it if `i3bar` had a way to make a block use all
remaining available space (`max_width` cannot do this), because I could
then align the focused-title block left. I'll look into implementing
that at some point.
