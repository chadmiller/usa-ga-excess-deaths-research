
all: ga-vital-records-offices-with-passwords.csv

ga-vital-records-offices.csv: ga-vital-records-offices.txt
	python3 txttocsv $< tmp
	@mv tmp $@

ga-vital-records-offices.txt: ga-vital-records-offices.pdf
	pdftotext -layout $< tmp
	@mv tmp $@

ga-vital-records-offices.pdf:
	wget -O tmp https://dph.georgia.gov/document/document/click-here-list-county-offices/download
	@mv tmp $@

ga-vital-records-offices-with-passwords.csv: ga-vital-records-offices.csv
	echo -n 'password,' >tmp
	head -n 1 $< >>tmp
	set -e; sed -n -e 2,\$$p $< |while read line; do p=$$(pwgen -BA 10); echo "$$p,$$line" >>tmp; done
	@mv tmp $@
	@echo
	@echo 'Now verify these and "mv $@ in-use-$@" to capture a precious file with passwords in it.'
	@echo 'Then "make letter-1.ps" to make the letter for org 1, and so on. How many orgs are there?'
	@sed -e 1d $@ |wc -l

letter-%.groff: in-use-ga-vital-records-offices-with-passwords.csv letter.groff.jinja2
	python3 filltemplate in-use-ga-vital-records-offices-with-passwords.csv $* letter.groff.jinja2 tmp
	@mv tmp $@

letter-%.ps: letter-%.groff
	tbl $< |groff -ms -Tps >tmp
	@mv tmp $@

clean:
	rm -vf ga-vital-records-offices.csv ga-vital-records-offices-with-passwords.csv ga-vital-records-offices.txt ga-vital-records-offices.pdf

