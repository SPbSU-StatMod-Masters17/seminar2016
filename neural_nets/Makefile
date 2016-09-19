file = text
all:
	make html
html:
	pandoc -f markdown -t html --filter pandoc-citeproc -s --smart --mathml --toc  ${file}.md -o ${file}.html
pdf:
	pandoc -f markdown -t latex --filter pandoc-citeproc --template russian.latex -s --smart --latex-engine=xelatex  ${file}.md -o ${file}.pdf

