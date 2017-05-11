# Pandoc-Markdown-Git Demo

## Why not Word?

Illustrate the abortion that Word is:

* create a text file: Hello World
* create a Word file: Hello World
* compare their sizes
* look at the code for helloworld.docx


## Markdown & Pandoc Demo

* create new `demodoc.md`
* demo using `pandoc` to convert to PDF, HTML, DOCX, etc.

	$ pandoc -o outputfile.ext inputfile.ext

* add some headers to the `demodoc.md`: H1, H2, H3
* use `pandoc` to convert to HTML and test links
* demo blockquote and lists
* use `pandoc` to convert to HTML and show rendering including TOC

	$ pandoc --toc -s -o outputfile.ext inputfile.ext

## Git
