This directory contains local patches to SudokuPad for testing.

The patches here are automatically applied to each cached copy of
SudokuPad downloaded, except if `NO_PATCH=1` is set when running
`bin/downloadsudokupad ...`

The files here should be named so that they will be sorted into the
correct order to apply when sorted alphanumerically.

Patches should be generated so that they can be applied with

patch -p1 <...

when in the download directory.
