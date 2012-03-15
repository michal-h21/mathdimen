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

Output without `matdhimen`:

<img width="841px" src="https://github.com/michal-h21/mathdimen/raw/master/images/without-mathdimen.png" alt="You can see wrong vertical position" />

and with `mathdimen`, using `svg` image for math

<img width="843px" src="https://github.com/michal-h21/mathdimen/raw/master/images/with-mathdimen.png" alt="Slightly better vertical alignment"/>

### Configure image conversion with `tex4ht.env`

Conversion of pictures from TeX's `dvi` format to outher formats like `png` or `svg` is configured in the file called `tex4ht.env`. 

The `env` file is stored in `%texmf-home%/texmf-dist/tex4ht/base/unix` or  `%texmf-home%/texmf-dist/tex4ht/base/win32`, depending on your operating system. For testing purposes, it is best to make copy of that file in your working directory. 

The structure of this file is little bit strange:

    <tag>
    Gsome command and the parameters
     Ganother command
    </tag>
     <anothertag>
    GNext command
     Gcommand again
     </anothertag>

Spaces are important. All lines with space at the beginning are ignored. Tagged sections are sometimes ignored. Quoting `tex4ht.env` itself:

>  Tagged script segments <tag>...</tag> are scanned only if their names are specified within -ctag switches of tex4ht.c and t4ht.c. When -c switches are not supplied, a -cdefault is implicitly assumed.

In the example, only `GNext command` is actually executed. If you look at actual `tex4ht.env` file, you can see that there are several configurations for output of images. Default is 

     <convert>
     G.png
     Gdvips -E -q -Ppdf -f %%1 -pp %%2 > zz%%4.ps
     Ggs -sDEVICE=pngalpha -sOutputFile=%%3 -r110x110 -dEPSCrop -dBackgroundColor=16#ffffff -dTextAlphaBits=2 -dGraphicsAlphaBits=2 -q -dbatch -dNOPAUSE zz%%4.ps -c quit
     Grm zz%%4.ps    
    G.svg
    Gdvips -Ppdf -mode ibmvga -D 110 -f %%1 -pp %%2 > zz%%4.eps
    Gpstoedit -f svg zz%%4.eps %%3      
    G.
    Gdvips -E -Ppdf -mode ibmvga -D 110 -f %%1 -pp %%2  > zz%%4.ps
    Gconvert -trim +repage -density 110x110 -transparent '#FFFFFF' zz%%4.ps %%3
     </convert>

lines 

    G.
    Gdvips -E -Ppdf -mode ibmvga -D 110 -f %%1 -pp %%2  > zz%%4.ps
    Gconvert -trim +repage -density 110x110 -transparent '#FFFFFF' zz%%4.ps %%3

means, that default conversion uses `dvips` command to convert picture to the eps file and then using convert to the final output format.

There is also section


    <dvipng>
    G.png
    Gdvipng -T tight -x 1400 -D 72 -bg Transparent -pp %%2:%%2 %%1 -o %%3
    G.gif
    Gdvipng -T tight -x 1400 -D 72 -bg Transparent -gif -pp %%2:%%2 %%1 -o %%3
    G.
    Gdvips -Ppdf -mode ibmvga -D 110 -f %%1 -pp %%2 > zz%%4.ps
    Gconvert -crop 0x0 -density 110x110 -transparent '#FFFFFF' zz%%4.ps %%3
    Grm zz%%4.ps
    </dvipng>

Which uses `dvipng` instead of `convert` and `dvips` and in my opinion the output is better. So to use this configuration, just erase spaces before `<convert>` and `</convert>`, and add space at the beginning of `<dvipng>` and `</dvipng>`

