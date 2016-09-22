file = text
all:
	make beamer
html:
	pandoc -f markdown -t html --filter pandoc-citeproc -s --smart --mathml --toc  ${file}.md -o ${file}.html
pdf:
	pandoc -f markdown -t latex --filter pandoc-citeproc -s --smart --template ../commons/markdown_templates/russian.latex --toc --latex-engine=xelatex  ${file}.md -o ${file}.pdf
beamer:
	pandoc -t beamer --template ../commons/markdown_templates/russian.beamer -s --smart --filter pandoc-citeproc slides.txt -o slides.pdf

