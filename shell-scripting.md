# Shell Scripting Notes

## Classic Shell Scripting book

Ctrl-D = EOF

### magic line

* specifies shell to use to run the program
* don't include any white space after options on the magic line

Example of magic line:

	#!/bin/sh -

Means: execute the program with the sh shell
The last hyphen means that there are no more options (security feature?)

Command line two dashes `--` means there are no more options to process

Execute two programs listed on one line sequentially by separating them with a semicolon (`;`)

Execute programs in parallel without waiting for completion by using an ampersand at the end of the line like this:

	$ blah &
	$ bleep &
	$ blorp &

The shell will run all of these in the background at the same time.

Commands that the shell executes that are not built-in cause the shell to generate a new "child shell" in which to run the program and then, when done, go back the parent shell and run the next command listed there.

### Variables

* name can start with underscore or letter

assigned like this:

    $ name="John"
    $ question="How old are you?"
    $ age=42

note the string is surrounded by quotes while the integer is not

See the contents of the variable by using `echo` followed by the dollar sign `$` smushed up against the variable name.

	$ echo $name

	> John

### `printf`

* does not add newline to end of line by default (needs a `\n` character)

format specifiers

* `%s` for strings
* `%d` for decimal int

Examples:

	$ printf "The sky '%s %s' \n" is blue

	> The sky is blue

	$ printf "%s is %d\n" $name $age

	> John is 42

### I/O Redirection

source: standard input
sink: standard output
place to list problems: standard error

default settings: terminal

Can be changed: redirection

Change standard input from terminal to a file with the (`<`) character.

	$ sort < unsorted-names.txt 

Change standard output from terminal to a file with the `>` character:

	$ cat unsorted-names.txt > newlist.txt

Take the input from a file `unsorted-names.txt` and output to another file `sorted-names.txt`.

	$ sort < unsorted-names.txt > sorted-names.txt

WARNING: if the file to which the output is being directed has stuff in it IT WILL BE DELETED!

If you want to harmlessly add stuff to the end of a file use the append operator `>>`.

	$ sort < unsorted-names.txt >> daily-attendance.txt

Use a pipeline to make the output of a program be the input of the next program:

	$ cat unsorted-names.txt | sort

`/dev/null` is a black hole into which you can send data. It is useful if you want to get the exit status of a program but you do not want its output.

`/dev/tty` is the terminal. You can redirect stuff to go to or come from the terminal.

To hide the echoing of typed characters use `stty -echo` to set tty echo off. Restore normal echoing with `stty echo`.

### Command searching

If there is no magic line in a program, the shell will look for the program in the users path, `$PATH`.

You can see which directories are in your path:

	$ echo $PATH

Add a directory (`newDirectory`) to your path by editing your `.profile` file. Append your directory to the end of the path you see listed:

`PATH=/home/username:/usr/bin:`**`/home/username/newDirectory`**

### Execution tracing

Turn on execution tracing to make the shell print each command when executed. This can be done as part of executing the script or it can be done within the script.

While executing:

	$ bash -x programName
	$ sh -x programName

Inside the program just include this line near the top.

	set -x

If you only need to debug a certain section you can this tracing off byin inserting this line:

	set +x

other cool options:

`set -e` will stop the program immediately if any external script exits non-zero (fail)
`set -n` check script for syntax errors
`strace` get a visual indication of what is happening
