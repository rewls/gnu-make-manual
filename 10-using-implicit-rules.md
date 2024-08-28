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

- Here is a catalogue of predefined implicit rules which are always available unless the makefile explicitly overrides or cancels them.

- This manual only documents the default rules available on POSIX-based operating systems.

- Compiling C programs

	- `*n*.o` is made automatically from `*n*.c` with a recipe of the form '`$(CC) $(CPPFLAGS) $(CFLAGS) -f`'.

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

- *All* implicit rules that do C compilation use '`$(CC)`' to get the program name for the compiler and *all* include '`$(CFLAGS)`' among the arguments given to the compiler.

- The variables used in implicit rules fall into two classes: those that are names of programs and those that contain arguments for the programs.

- If a variable value contains more than one argument, separate them with spaces.

- The following tables describe of some of the more commonly-used predefined variables.

- This list is not exhaustive, and the default values shown here may not be what `make` selects for your environment.

- To see the complete list of predefined variables for your instance of GNU `make` you can run '`make -p`' in a directory with no makefiles.

- Here is a table of some of the more common variables used as names of programs in built-in rules:

	- `AR`: Archive-maintaining program; default '`ar`'.

	- `CC`: Program for compiling C programs; default '`cc`'.

	- `ARFLAGS`: Flags to give the archive-maintaining program; default '`rv`'.

	- `CFLAGS`: Extra flags to give to the C compiler.

	- `CPPFLAGS`: Extra flags to give the C preprocessor and programs that use it.

	- `LDFLAGS`: Extra flags to give to compilers when they are supposed to invoke the linker, '`ld`', such as `-L`.

		- Libraries should be added to the `LDLIBS` variable instead.

	- `LDLIBS`: Library flags or names given to compilers when they are supposed to invoke the linker, `ld`.

		- `LOADLIBES` is a deprecated alternative to `LDLIBS`.

		- Non-library linker flags should go in the `LDFLAGS` variable.

## 10.5 Defining and Redefining Pattern Rules

- You define an implicit rule by writing a *pattern rule*.

- A pattern rule looks like an ordinary rule, except that its target contains the character '`%`'.

- The target is considered a pattern for matching file names; the '`%`' can match any nonempty substring, while other characters match only themselves.

- The prerequisites likewise use '`%`' to show how their names relate to the target name.

- Note that expansion using '`%`' in pattern rules occurs **after** any variable or functin expansions, which take place when the makefile is read.

### 10.5.3 Automatic Variables

- The *automatic variables* have values computed afresh for each rule that is executed, based on the target and prerequisites of the rule.

- It's very important that you recognize the limited scope in which automatic variable values are available: they only have values within the recipe.

- Here is a table of automatic variables:

	- `$@`

		- The file name of the target of the rule.

	- `$<`

		- The name of the first prerequisites.

	- `$?`

		- The names of all the prerequisites that are newer than the target, with spaces between them.

		- If target does not exist, all prerequisites will be included.

	- `$^`

		- The names of all the prerequisites, with spaces between them.
