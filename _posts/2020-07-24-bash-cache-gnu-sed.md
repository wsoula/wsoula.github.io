Bash Cache and GNU Sed
---

I never knew this but if you run a command, `sed`, it will lookup in the path where
that executable is and then store it in a cache so it doesn't have to look it up
again.  You can see this cache by running the `hash` command.  The article [1] says
to run `hash -t sed` and `hash -d sed` to see the current location and remove it, but
that doesn't work on Mac.  I had to run `unhash sed`.  This all came from trying to
use gnu sed on mac by default from this article [2] where I needed to update my PATH
to `PATH="/usr/local/opt/gnu-sed/libexec/gnubin:$PATH"` which I got from
`brew info gnu-sed` after running `brew install gnu-sed`

[1] - https://unix.stackexchange.com/questions/335801/bash-remembers-wrong-path-to-an-executable-that-was-moved-deleted
[2] - https://gist.github.com/andre3k1/e3a1a7133fded5de5a9ee99c87c6fa0d
