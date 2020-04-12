# Input/Output Redirection

## Three Components

* `stdin` - typically the keyboard
* `stout` - typically the display
* `stderr` - system default for error output

## Output Redirection

* `>` - (single 'greater than' symbol) is used to send the output of a command to a file **IMPORTANT**: ALL existing file contents will be erased and replaced with the output of the command
* `>>` - (double 'greather than') is used to _append_ the output of a command to a file
* `<` - (single 'less than') is used to import the contents of the file into the command line - you can imagine using this for some command that you absolutely have to get right that already exists as text in a file
* `|` - pipes are used to take the output of one command and use it as the input into another command

## Example

Imagine you had a file that contained thousands of email address - one on each line - that you need to deduplicate and sort in alphabetical order.

You can quickly do this by using some redirection:

	cat unsortedUndedupedList | sort | uniq > sortedList

Let's break this apart.

`cat` is used to print out the contents of the unsorted list. If I had ended the command there, I would see a monstrously-long listing of email addresses fly by on `stdout` - in this case my screen.

However, I used a pipe to send that output - bypassing `stdout` directly into my next command, `sort`. Sort does what you think it should do.  Had I ended the command here, I would have my email list nicely sorted in alphabetical order.

However, I used a pipe to sent that output into the next command, `uniq`. `uniq` eliminates any duplicate line items.

Lastly, the output of `uniq` is sent to a file named `sortedList`.

Unix graybeards will be shouting at me right now because both `sort` and `uniq` have a little bit of overlap in functions and there is probably a shorter, more efficient way to accomplish this task. I used these two programs, however, in the name of simplifying this for someone who has never seen this before.
