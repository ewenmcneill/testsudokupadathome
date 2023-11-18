# Test SudokuPad at Home

[SudokuPad](https://sudokupad.app/) is a web based Sudoku puzzle solving
interface devleoped by [Sven Neumann](https://svencodes.com/), and used
extensively by the [Cracking the Cryptic](https://crackingthecryptic.com/)
YouTube Channel and related puzzle creators, such as on the [Cracking
the Cryptic Discord](https://discord.gg/BbN89j5) (see [CtC Discord
History](https://crackingthecryptic.fandom.com/wiki/CTC_Fan_Discord)).

SudokuPad is *not* open source software, but since it is a JavaScript
web application provided to a web browser with unminified source code it
might be best described as a "source available" application.  Every
interaction with SudokuPad to solve a puzzle involves downloading, and
caching, the entire source code.  As a result multiple people have
contributed fixes to SudokuPad, several of whom are listed at the top
of, eg `script.js`.

Periodically it is necessary to test SudokuPad "at home" in order to
make local fixes, or work around bugs in SudokuPad until they are fixed
upstream.

This repository provides some "developer level" tools for caching a local
copy of SudokuPad, with local patches automatically applied, and then
serve them up with a local webserver.  It was written to work on an Ubuntu
Linux system, particularly to help locally work around [a long standing
bug in SudokuPad with handling the control modifier key in Firefox on
Linux](https://discord.com/channels/709370620642852885/789547910844514376/1159998193174589492);
the first patch in the `patches` directory is a
couple of keyboard handling fixes (first [posted as a
gist](https://gist.github.com/ewenmcneill/b8284e0341053f1be0e4b6643dc10ee2),
2023-10-07).

The code in this repository is MIT licensed (see [LICENSE](LICENSE));
SudokuPad remains copyright Sven Neumann.  If you like
SudokuPad you might consider [supporting Sven Neumann on
Patreon](https://patreon.com/svencodes).


## Usage

Clone this git repository to your local computer.  Make sure you have
`python3`, `patch`, `screen`, and `wget` installed (typically all are
installed on most developer worktations).

Cache a local copy of the SudokuPad version you want to base your development
on with someting like:

    bin/downloadsudokupad app.crackingthecryptic.com

which by default will create a directory called
`app.crackingthecrypt.com-VERSION` alongside the `bin` directory, and
apply the patches in `patches` to it (run with `NO_PATCH=1 ...` if you
want to skip the automatic patching).  If you wish to follow the
"bleeding edge" of SudokuPad development, the download tool should also
be able to download from `beta.sudokupad.app` instead.

Make sure the version you want to test locally is symlinked to
`local-sudokupad`:

    ln -s "$(ls -td app.crackingthecryptic.com-0.* | head -1)" local-sudokupad

Then run the webserver to serve up `local-sudokupad` with a webserver
on `locahost` (by default at `http://localhost:9876/`):

    bin/runsudokupad

By default it will run in `screen` session, so you do not have to keep
a terminal window constantly open for it.  (If you want to avoid that,
for testing purposes, set `STY=1` when running `bin/sudokupad`, and it
will assume it is already in a `screen` session and not start a new one;
that is the `screen` marker for a screen tty.)

Then to play puzzles with the local version, use `bin/playsudokupad` to
convert the puzzle link into one that references the local webserver,
and put that into the cut'n'paste buffer, ready to past into a browser
tab.  For instance (some GAS puzzle links):

    bin/playsudokupad https://tinyurl.com/vc7xu34w
    bin/playsudokupad https://sudokupad.app/sudoku/BLLGjtrb4P
    bin/playsudokupad https://sudokupad.app/clover/nov-16-2023-numbered-rooms

For `tinyurl.com` links it will fetch the full link from `tinyurl.com`
and then rewrite the link to point at `localhost:9876` (and cache the
full link in `puzzles/tinyurl.com/SHORTCODE` so you only have to download
the full link once).

For `sudokupad.app` and `app.crackingthecryptic.com` links it will just
rewrite the link to point at `localhost:9876`.  If the SudokuPad puzzle
shortcode system is used, then the webserver in `bin/runsudokupad` will
fetch the full puzzle from the online SudokuPad server, and cache it
in `puzzles/sudokupad/SHORTCODE` when your webbrowser loads the puzzle.

You might also be able to use
[GreaseMonkey](https://addons.mozilla.org/en-US/firefox/addon/greasemonkey/),
[TamperMonkey](https://www.tampermonkey.net/), or
[ViolentMonkey](https://violentmonkey.github.io/) to intercept the browser
loading the main SudokuPad URLs and redirect them to your local version.
For now I have kept using `playsudoku` to do the URL rewrites, despite
the manual steps, because it keeps open the option of doing side by side
testing with the online SudokuPad versions.

Since the SudokuPad configuration is stored in a cookie under the domain
you are using, the first time you use the local cachec copy, all your
settings will reset to the defaults as of the version of SudokuPad
you cached.  You can either visit the settings cog and set them again
for your local copy, or use the settings cog "Import Settings" /
"Export Settings" features to copy over your settings to the new domain.
