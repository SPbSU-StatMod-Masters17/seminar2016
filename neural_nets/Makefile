file = text
all:
	make beamer
html:
	pandoc -f markdown -t html --filter pandoc-citeproc -s --smart --mathml --toc  ${file}.md -o ${file}.html
pdf:
	pandoc -f markdown -t latex --filter pandoc-citeproc -s --smart --template russian.latex --toc --latex-engine=xelatex  ${file}.md -o ${file}.pdf
beamer:
	pandoc -t beamer --template russian.beamer -s --smart --filter pandoc-citeproc slides.txt -o slides.pdf

