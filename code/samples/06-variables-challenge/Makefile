COUNT_SRC=wordcount.py
COUNT_EXE=python $(COUNT_SRC)

# Count words.
%.dat : books/%.txt $(COUNT_SRC)
	$(COUNT_EXE) $< $@

.PHONY : clean
clean :
	rm -f *.dat
	rm -f analysis.tar.gz

.PHONY : dats
dats : isles.dat abyss.dat last.dat

analysis.tar.gz : *.dat $(COUNT_SRC)
	tar -czf $@ $^
