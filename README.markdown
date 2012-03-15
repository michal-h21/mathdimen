# Mathdimen

With `tex4ht`, it is possible to convert LaTeX files to various output formats. Certain elements with difficult formating, typically math content, can be converted to images. Unfortunately, when these images are used in the running text, they can have wrong vertical placement or wrong size.

This package enables to precisely place these converted images, in terms of the vertical alignment and correct setting of the dimensions. 

## Usage

There are four steps needed for correct use of this package

1. In your document, use package `mathdimen` For math you must use `\( \)` or `\[ \]` commands. commands `$` and `$$` are not supported
2. Create custom file `tex4ht.env` and configure `dvi` to image conversion 
3. [Optional] Create custom configuration file and change the appearance of tags
4. You must run first `latex` or `pdflatex` and then `htlatex` (you must run `LaTeX` only when you change some math, this step is needed for saving of correct dimensions)
 
### Sample file 
```LaTeX
\documentclass[12pt]{article}
\usepackage[utf8]{inputenc}
\usepackage{tgtermes}
\usepackage[T1]{fontenc}
\usepackage[]{mathdimen}

\begin{document}
Sample text with some math inside \(c=\sqrt{a^2+b^2}\). Text now continues. \(\sqrt[n]{1+x+x^2+x^3+\ldots}\)
\end{document}
```


