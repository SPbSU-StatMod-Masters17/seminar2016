file = text
all:
	make html
html:
	pandoc -f markdown -t html --filter pandoc-citeproc -s --smart --mathml --toc  ${file}.md -o ${file}.html
pdf:
	pandoc -f markdown -t latex --filter pandoc-citeproc -s --smart --template russian.latex --toc --latex-engine=xelatex  ${file}.md -o ${file}.pdf

