file = text
all:
	make html
html:
	pandoc -f markdown -t html --filter pandoc-citeproc -s --smart  ${file}.md -o ${file}.html
pdf:
	echo "Not supported yet because say hello to Russian"
	# pandoc -f markdown -t latex --filter pandoc-citeproc -s --smart  text.md -o text.pdf
