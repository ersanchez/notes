# Regular Expressions

Purpose: find patterns in text strings

* `\d` any digit 0 to 9 - character escape or character shorthand
* `\D` any character not a digit (capital = not)?
* `.` "period" match any character - wildcard for a single character; matches everything but a line ending
* `?` means the preceding character is optional
* `+` one or more
* `\*` zero or more
* `^` beginning of line
* `|` alternative; OR
* `$` matches end of line

quantifier (number in curly braces) specifies exactly how many of a character you want

	\d{3}-\d{2}-\d{4}

will find a social security pattern: 3 digits hyphen 2 digits hyphen 4 digits

	\d{3}-?\d{2}-?\d{4}
