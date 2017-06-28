slides.html: slides.Rmd
	Rscript -e 'rmarkdown::render("$<", output_file="$@")'

README.md: slides.Rmd
	Rscript -e 'rmarkdown::render("$<", output_file="$@", output_format="github_document")'
