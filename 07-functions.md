---
layout: page
title: Automation and Make
subtitle: Functions
minutes: 0
---

> ## Learning Objectives {.objectives}
>
> * Use Make's `wildcard` function to get lists of files matching a
>   pattern. 
> * Use Make's `patsubst` function to rewrite file names.

Make has many [functions](reference.html#function) which can be used to
write more complex rules. One example is `wildcard`. `wildcard` gets a
list of files matching some pattern, which we can then save in a
variable. So, for example, we can get a list of all our text files
(files ending in `.txt`) and save these in a variable:

~~~ {.make}
TXT_FILES=$(wildcard books/*.txt)
~~~

We can add `.PHONY` target and rule to show the variable's value:

~~~ {.make}
.PHONY : variables
variables:
	@echo TXT_FILES: $(TXT_FILES)
~~~

> ## @echo {.callout}
>
> Make prints actions as it executes them. Using `@` at the start of
> an action tells Make not to print this action. So, by using `@echo`
> instead of `echo`, we can see the result of `echo` (the variable's
> value being printed) but not the `echo` command itself.

If we run Make:

~~~ {.bash}
$ make variables
~~~

We get:

~~~ {.output}
TXT_FILES: books/abyss.txt books/isles.txt books/last.txt books/sierra.txt
~~~

Note how `sierra.txt` is now processed too.

`patsubst` ('pattern substitution') takes a list of names and rewrites
these according to a pattern. Again, we can save the result in a
variable. So, for example, we can rewrite our list of text files into
a list of data files (files ending in `.dat`) and save these in a
variable:

~~~ {.make}
DAT_FILES=$(patsubst books/%.txt, %.dat, $(TXT_FILES))
~~~

We can extend `variables` to show the value of `DAT_FILES` too:

~~~ {.make}
.PHONY : variables
variables:
	@echo TXT_FILES: $(TXT_FILES)
	@echo DAT_FILES: $(DAT_FILES)
~~~

If we run Make,

~~~ {.bash}
$ make variables
~~~

then we get:

~~~ {.output}
TXT_FILES: books/abyss.txt books/isles.txt books/last.txt books/sierra.txt
DAT_FILES: abyss.dat isles.dat last.dat sierra.dat
~~~

With these we can rewrite `clean` and `data`:

~~~ {.make}
.PHONY : clean
clean :
        rm -f $(DAT_FILES)
        rm -f analysis.tar.gz

.PHONY : dats
dats : $(DAT_FILES)
~~~

Let's check:

~~~ {.bash}
$ make clean
$ make dats
~~~

We get:

~~~ {.output}
python wordcount.py books/abyss.txt abyss.dat
python wordcount.py books/isles.txt isles.dat
python wordcount.py books/last.txt last.dat
python wordcount.py books/sierra.txt sierra.dat
~~~

We can also rewrite `analysis.tar.gz` too:

~~~ {.make}
analysis.tar.gz : $(DAT_FILES) $(COUNT_SRC)
	tar -czf $@ $^
~~~

If we re-run Make:

~~~ {.bash}
$ make clean
$ make analysis.tar.gz
~~~

We get:

~~~ {.output}
$ make analysis.tar.gz
python wordcount.py books/abyss.txt abyss.dat
python wordcount.py books/isles.txt isles.dat
python wordcount.py books/last.txt last.dat
python wordcount.py books/sierra.txt sierra.dat
tar -czf analysis.tar.gz abyss.dat isles.dat last.dat sierra.dat wordcount.py
~~~

We see that the problem we had when using the bash wild-card, `*.dat`,
which required us to run `make dats` before `make analysis.tar.gz` has
now disappeared, since our functions allow us to create `.dat` file
names from those `.txt` file names in `books/`.
