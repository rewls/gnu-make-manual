# 10 Using Implicit Rules

- *Implicit rules* tell `make` how to use customary techniques so that you do not have to specify them in detail when you want to use them.

- File names determine which implicit rules are run.

- The built-in implicit rules use several variables in their recipes so that, by chaning the values of the variables, you can change the way the implicit rule works.

- For example, the variable `CFLAGS` controls the flags given to the C compiler by the implicit rule for C compilation.

	- See Section 10.3 [Variables Used by Implicit Rules].

## 10.1 Using Implicit Rules

- To allow `make` to find a customary method for updating a target file, all you have to do is refrain from specifying recipes yourself.

- Either write a rule with no recipe, or don't write a rule at all.

- Then `make` will figure out which kind of source file exists or can be made.

- If an implicit rule is found, it can supply both a recipe and one or more prerequisites.

- Each implicit rule has a target pattern and prerequisite patterns.

- See Section 10.2 [Catalogue of Built-In Rules] for a catalogue of all the predefined implicit rules.

## 10.2 Catalogue of Built-In Rules

m:w
i- Here is a catalogue of predefined implicit rules which are always available unless the makefile explicitly overrides or cancels them.

- This manual only documents the default rules available on POSIX-based operating systems.

- Compiling C programs

	- `*n*.o` is made automatically from `*n*.c` with a recipe of the form '`$(CC) $(CPPFLAGS) $(CFLAGS) -f'.

- Linking a single object file

	- `*n*` is made automatically from `*n*.o` by running the C compiler to link the program.

	- The precise recipe used is '`$(CC) $(LDFLAGS) *n*.o $(LOADLIBES) $(LDLIBS)`'.

	- This rule does the right thing for a simple program with only one source file.

	- It will also do the right thing if there are multiple object files, one of which has a name matching that of the executable file.

	- Thus,

		```Makefile
		x: y.o z.o
		```

	- when `x.c`, `y.c` and `z.c` all exist will execute:

		```Makefile
		cc -c x.c -o x.o
		cc -c y.c -o y.o
		cc -c z.c -o z.o
		cc x.o y.o z.o -o x
		rm -f x.o
		rm -f y.o
		rm -f z.o
		```

	- In more complicated cases, such as when there is no object file whos name derives from the executable file name, you must write an explicit recipe for linking.

## 10.3 Variables Used by Implicit Rules

- You can alter the values of these variables in the makefile, with arguments to `make`, or in the environment to alter how the implicit rules work without redefining the rules themselves.
