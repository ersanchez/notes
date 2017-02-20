# Shell Scripting Notes

## Classic Shell Scripting book

Ctrl-D = EOF

magic line

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

Variables

* name can start with underscore or letter

assigned like this:

    $ name="John"
    $ question="How old are you?"
    $ age=42

note the string is surrounded by quotes while the integer is not

See the contents of the variable by using `echo` followed by the dollar sign `$` smushed up against the variable name.

	$ echo $name

	> John

**`printf`**

* does not add newline to end of line by default (needs a `\n` character)

format specifiers

* `%s` for strings
* `%d` for decimal int

Examples:

	$ printf "The sky '%s %s' \n" is blue

	> The sky is blue

	$ printf "%s is %d\n" $name $age

	> John is 42
