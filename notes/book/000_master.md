---
title: |
	\Huge\bf\colorbox{munsell}{\color{white} Administration et gestion des réseaux}
author: \color{white}\huge\bf Pierre Bettens
date: \color{white}\textit{Un grand pouvoir implique de grandes responsabilités}
abstract: |
	version _alpha_
geometry: margin=3cm
fontsize: 12pt
highlight-style: tango
documentclass: article
header-includes:	
	- \usepackage[utf8]{inputenc}
	- \usepackage{wallpaper}
    - \usepackage{minitoc}
	- \usepackage{pdfpages}
	- \usepackage[font=small,labelfont=sc,width=.8\textwidth, labelsep=endash]{caption}
	- \usepackage{url}
	- \usepackage[french]{datetime2}
	- \usepackage{amsfonts}
	- \usepackage{supertabular}
	- \usepackage{multicol}
	- \usepackage{supertabular}
	- \usepackage{xcolor}
	- \usepackage{makeidx}
	- \usepackage[columns=3]{idxlayout}
	- \makeindex
	- \usepackage{enumitem}
	- \setlist[itemize]{leftmargin=*}
	- \setlength{\columnsep}{11mm} 
	- \setcounter{secttocdepth}{3}
	- \renewcommand{\stctitle}{}
	- \interfootnotelinepenalty=10000
	- \renewcommand{\abstractname}{}
	- \renewcommand{\contentsname}{}
	- |
	  ```{=latex}
	  \newcommand{\btwoc}{\begin{multicols}{2}}
	  \newcommand{\etwoc}{\end{multicols}}
	  \newcommand{\yaline}{\nopagebreak[4]\mbox{}\hfill\rule{.33\linewidth}{.3pt}\hfill\mbox{}}
	  ```
	- \usepackage{fancyhdr}
	- \usepackage{lastpage}
	- \usepackage{fontawesome}
	- \usepackage{titleref}
	- \usepackage{tcolorbox}
	- \definecolor{munsell}{HTML}{0093AF}
	- \definecolor{in}{HTML}{dae9ec}
	- \newtcolorbox{myquote}{colback=in, colframe=munsell}
	- \renewenvironment{quote}{\begin{myquote}}{\end{myquote}}
	- \newtcolorbox{intro}{width=\linewidth-2cm, center, colframe=white}
	- |
	  ```{=latex}
	  \newcommand{\bintro}{\begin{intro}}
	  \newcommand{\eintro}{\end{intro}}
	  ```
	#- \bibliographystyle{abbrv} not working, use csd
	- \usepackage[french]{varioref}
bibliography: 
	- pitBiblio.bib 
	- pitLiens.bib
#csl: resources/yet-another.csl 
#csl: resources/apa.csl
csl: resources/apa7-fr.csl
nocite: |
	@mcd-info151
link-citations: true
monofont: DejaVuSansMono.ttf 
mathfont: texgyredejavu-math.otf 
---

<!-- ajout d'une image et du logo -->
\ThisULCornerWallPaper{1}{resources/images/vic20.jpg}
<!--\vspace{-10mm}
\begin{flushright}
\includegraphics[width=40mm]{resources/images/HE2B-Logo-ESI.png}
\end{flushright}
-->

\thispagestyle{empty}

```{=latex}
\dosecttoc
\setcounter{tocdepth}{1}
\renewcommand{\baselinestretch}{0.75}\normalsize
\tableofcontents
\renewcommand{\baselinestretch}{1.0}\normalsize
```

\pagestyle{fancy}
\lhead{}
\chead{}
\rhead{\it\currenttitle}
\lfoot{\it pbt\hspace{-5pt}\includegraphics{resources/images/k-s.png}}
\cfoot{}
\rfoot{\faStreetView\;\thepage\,/\,\pageref{LastPage}}
\renewcommand{\headrulewidth}{0pt}

