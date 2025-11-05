# NoahAvrEnv

Note: you may have to update the "AVRDUDE" value in the makefile if your avrdude.exe is in a different directory.

Open VScode in the top level directory "NoahAvrEnv" so that VScode can access the AVR register names.

Make Commands:

    $ make

        - Builds the files in src and its subdirectories

    $ make clean

        - Deletes build artifacts

    $ make flash

        - Runs the AVR dude flash command found in the makefile
