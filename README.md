rpatool
=======

This is a simple tool allowing you to create, modify and extract Ren'Py Archive (.rpa/.rpi) files.
Currently, only writing to RPAv2/RPAv3 archives is supported.

Usage
-----
    rpatool [-l] [-x] [-c] [-d] [-a] [-o OUTFILE] [-2] [-3] [-k KEY]
            [-p COUNT] [-h] [-v] [-V]
            ARCHIVE [FILE [FILE ...]]


    positional arguments:
      ARCHIVE               The Ren'py archive file to operate on
      FILE                  Zero or more files to operate on
    
    action arguments:
      -l, --list            List files in archive ARCHIVE
      -x, --extract         Extract FILEs from ARCHIVE
      -c, --create          Creative ARCHIVE from FILEs
      -d, --delete          Delete FILEs from ARCHIVE
      -a, --append          Append FILEs to ARCHIVE
    
    optional arguments:
      -o OUTFILE, --outfile OUTFILE
                            An alternative output archive file when appending to or
                            deleting from archives, or output directory when extracting.
      -2, --two             Use the RPAv2 format for creating/appending to
                            archives
      -3, --three           Use the RPAv3 format for creating/appending to
                            archives (default)
      -k KEY, --key KEY     The obfuscation key used for creating RPAv3 archives 
                            (default: 0xDEADBEEF)
      -p COUNT, --padding COUNT
                            The maximum number of bytes of padding to add between
                            files (default: 0)
      -h, --help            Print this help and exit
      -v, --verbose         Be a bit more verbose while performing operations
      -V, --version         Show version information
    
    The FILE argument can optionally be in ARCHIVE=REAL format, mapping a file in
    the archive file system to a file on your real file system. An example of
    this is: rpatool -x test.rpa script.rpyc=/home/foo/test.rpyc

Examples
--------
    rpatool -x foo.rpa
Will extract every file from `foo.rpa`into the current directory, making subdirectories when necessary.

    rpatool -o output -x foo.rpa script.rpyc ui.png
Will extract the files `script.rpyc` and `ui.png` from `foo.rpa` into the directory `output`.

    rpatool -c bar.rpa test.jpg script.rpy sprites
Will create the archive `bar.rpa`, containing the files `test.jpg`, `script.rpy` and the directory `sprites`.

    rpatool -p 25 -k 12345 -c bar.rpa movies=C:\projects\vn\movies
Will create the archive `bar.rpa` with the obfuscation key `0x12345` and maximum padding of `25`, taking files from `C:\projects\vn\movies` and placing them in the archive folder `movies`.

    rpatool -l baz.rpa
Will list all files in the archive `baz.rpa`.

    rpatool -v -a foo.rpa sprites=sprites_new
Will add all files from the directory `sprites_new` to the directory `sprites` in the archive, giving more information about what it's doing.

    rpatool -o bar_new.rpa -d bar.rpa foo.jpg
Will remove the file `foo.jpg` from the archive `bar.rpa`, storing the result archive in `bar_new.rpa`.

API
---
`rpatool` can also be included in any other project (following the license conditions, of course) to provide the `RenPyArchive` class.
A small overview:

    RenPyArchive([file = None], [version = 3], [padlength = 0], [key = 0xDEADBEEF], [verbose = False])
The constructor, which will optionally load an archive file.

`file`: the archive file to open. If None, no archive will be attempted to open.

`version`: the archive format version used to save the archive when `RenPyArchive.save([file])` is called. Default: 3

`padlength`: the maximum number of bytes of padding to put between files when saving. Default: 0

`key`: the obfuscation key used when saving RPAv3 archives. Default: 0xDEADBEEF

`verbose`: print info on what we are doing to the command line. Default: False

    RenPyArchive.load(filename)
Loads an archive file from `filename`. Will raise an `IOError` if the file can't be accessed, or a `ValueError` if the file is not detected as a Ren'Py archive.

    RenPyArchive.save([filename])
Save the archive to `filename`. Will raise `ValueError` if the filename isn't given with `filename`, nor previously defined, or an `IOError` if it couldn't save the file.
    
    RenPyArchive.list()
Give a list of all filenames currently in the archive.

    RenPyArchive.has_file(filename)
Returns True if `filename` is found in the archive, False otherwhise.

    RenPyArchive.add(filename, content)
Add a file to the archive with file `filename` and contents `content`. Will raise a `ValueError` if the filename already exists in the archive.

    RenPyArchive.change(filename, content)
Change the contents of a current file in the archive. Will raise an `IOError` if the file isn't known in the archive.

    RenPyArchive.remove(filename)
Remove `filename` from the archive. Will raise an `IOError` if the filename isn't known in the archive.

    RenPyArchive.read(filename)
Read and return the content of file `filename` in the archive. Will raise an `IOError` if the filename isn't known in the archive.

License
-------
rpatool is dual-licensed under the 3-clause BSD/'new BSD' license and the WTFPL. See the LICENSE file for more details.

Disclaimer
----------
This tool is intended for use with files on which the authors allowed modification of and/or extraction from ONLY and the unpermitted use on files where such consent was not given is highly discouraged, and most likely a license violation as well.
Support requests for help with dealing with such files will not be answered.

Credits
-------
Credits for the creation of the Ren'Py archive format and the reference code in Ren'Py go to renpytom.
