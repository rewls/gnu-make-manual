# 4 Writing Rules

- A *rule* appears in the makefile and says when and how to remake certain files, called the rule's targets.

- It lists the other files that are the *prerequisites* of the target, and the *recipe* to use to create or update the target.

- The order of rules is not significant, except for determining the *default goal*.

- The default goal s the first target of the first rule in the first makefile.

- There are two exceptions: a target starting with a period is not a default unless it also contains one or more slashes; and, a target that defines a pattern rule has no effect on the default goal.

	- See Section 10.5 [Defining and Redefining Pattern Rules].

- Therefore, we usually write the makefile so that the first rule is the one for compiling the entire program or all the programs described by the makefile (often with a target called '`all`')..

	- See Section 9.2 [Arguments to Specify the Goals].

## 4.1 Rule Syntax

- In general, a rule looks like this:

	```Makefile
	*targets*: *prerequisites*
		*recipe*
		...
	```

- or like this:

	```Makefile
	*targets*: *prerequisites*; *recipe*
		*recipe*
		...
	```

- The *targets* are file names, seperated by spaces.

- Wildcard characters may be used and a name of the form `*a*(*m*)` represents member *m* in archive file *a*.

	- See Section 4.3 [Using Wildcard Characters in File Names].

	- Section 11.1 [Archive Members as Targets].

- Usually there is only one target per rule, but occasionally there is a reason to have more.

	- See Section 4.9 [Multiple Targets in a Rule].

- The *recipe* lines start with a tab character.

- The first recipe line may appear on the line after the prerequisites, with a tab character, or may appear on the same line, with a semicolon.

- Either way, the effect is the same.

- There are other differences in the syntax of recipes.

	- See Chapter 5 [Writing Recipes in Rules].

- Because dollar signs are used to start `make` variable references, if you really want to a dollar sign in a target or prerequisite you must write two of them.

	- See Chapter 6 [How to Use Variables].

- If you have enabled secondary expansion and you want a literal dollar sign in the prerequisites list, you must actually write *four* dollar sign.

- You may split a long line by inserting a backslash followed by a newline, but this is not required, as `make` places no limit on the length of a line in a makefile.

- A rule tells `make` two things: when the targets are out of date, and how to update them when necessary.

- The criterion for being out of date is specified in terms of the *prerequisites*, which consist of file names separated by spaces. (Wildcards and archive members are allowed here too.)

	- See Chapter 11 [Archives].

- A target is out of date if it does not exist or if it is older than any of the prerequisites.

- How to update is specified by a *recipe*.

- This is one or more lines to be executed by the shell, but with some extra features.

	- See Chapter 5 [Writing Recipes in Rules].

## 4.2 Types of Prerequisites

- There are two different types of prerequisites understood by GNU `make`: normal prerequisites, described in the previous section, and *order-only* prerequisites.

- A normal prerequisite makes two statements: first, it imposes an order in which recipes will be invoked: the recipes for all prerequisites of a target will be completed before the recipe for the target is started.

- Second, it imposes a dependency relationship: if any prerequisite is newer than the target, then the target is considered out-of-date and must be rebuilt.

- Occasionally you may want to ensure that a prerequisite is built before a target, but *without* forcing the target to be updated if the prerequisite is updated.

- *Order-only* prerequisites are used to create this type of relationship.

- Consider an example where your targets are to be placed in a separate directory, and that directory migh not exist before `make` is run.

- In this situation, you want the directory to be created before any targets are placed into it but, because the timestamps on directories change where a file is added, removed, or renamed, we certainly don't want to rebuild all the targets whenever the directory's timestamp changes.

- One way to manage this is with order-only prerequisites: make the directory an order-only prerequisite on all the targets:

```Makefile
OBJDIR := objdir
OBJS := (addprefix $(OBJDIR)/,foo.o bar.o baz.o)

$(OBJDIR)/%.o : %.c
	$(COMPILE.c) $(OUTPUT_OPTION) $<

all: $(OBJS)

$(OBJS): | $(OBJDIR)

$(OBJDIR):
	mkdir $(OBJDIR)
```

- Now the rule to create the `objdir` directory will be run, if needed, before any, before any '.o' is built, but no '.o' will be built because the `objdir` directory timestamp changed.

## 4.3 Using Wildcard Characters in File Names

- A single file name can specify many files using *wildcard characters*.

- The wildcard characters in `make` are '`*`', '`?`' and '`[...]`', the same as in the Bourne shell.

- If an expression matches multiple files then the results will be sorted.

- However multiple expressions will not be globally sorted.

- The character '`~`' at the beginning of a file name also has special significance.

- If alone, or followed by a slash, it represents your home directory.

- If the '`~`' is followed by a word, the string represents the home directory of the use named by that word.

- Wildcard expansion is performed by `make` automatically in targets and in prerequisites.

- In recipes, the shell is responsible for wildcard expansion.

- In other contexts, wildcard expansion happens only if you request it explicitly with the `wildcard` function.

- The special significance of a wildcard characters can be turned off by preceding it with a backslash.

### 4.3.1 Wildcard Examples

- With the following rule in the makefile, '`make print`' will print all the '`.c`' files that have changed since the last time you printed them:

	```Makefile
	print: *.c
		lpr -p $?
		touch print
	```

- The automatic variable '`$?`' is used to print only those files that have changed; see Section 10.5.3 [Automatic Variables].

### 4.3.3 The Function `wildcard`

- Wildcard expansion does not normally take place when a variable is set, or inside the arguments of a function.

- If you want to do wildcard expansion in such places, you need to use the `wildcard` function, like this:

	```Makefile
	$(sildcard *pattern*...)
	```

- This string, used anywhere in a makefile, is replaced by a space-separated list of names of existing files that match one of the given file name patterns.

- If no existing file name matches a pattern, then that pattern is omitted from the output of the `wildcard` function.

- The results of the `wildcard` function are sorted.

- Each individual expression is sorted separately.

- One use of the wildcard function is to get a list of all the C source files in a directory, like this:

	```Makefile
	$(wildcard *.c)
	```

- We can change the list of C source files into a list of object files by replacing the '`.c`' suffix with '`.o`' in the result, like this:

	```Makefile
	$(patsubst %.c,%.o,$(wildcard *.c))
	```

	- See Section 8.2 [Functions for String Substitution and Analysis].

- Thus, a makefile to compile all C source files in the directory and then link them together could be written as follows:

	```Makefile
	objects := $(patsubst %.c,%.o,$(wildcard *.c))

	foo: $(objects)
		cc -o foo $(objects)
	```

- See Section 6.2 [The Two Flavors of Variables] for an explanation of '`:=`', which is a variant of '`=`'.

## 4.4 Searching Directories for Prerequisites

- For large systems, it is often desirable to put sources in a separate directory from the binaries.

- The *directory search* features of `make` facilitate this by searching several directories automatically to find a prerequisite.

- When you redistribute the files among directories, you do not need to change the individual rules, just the search paths.

## 4.5 Phony Targets

- A phony target is one that is not really the name of a file; rather it is just a name for a recipe to be executed when you make an explicit request.

- There are two reasons to use a phony target: to avoid a conflict with a file of the same name, and to improve performance.

- If you write a rule whose recipe will not create the target file, the recipe will be executed every time the target comes up for remaking.

- Here is an example:

	```Makefile
	clean:
		rm *.o temp
	```

- In this example, the `clean` target will not work properly if a file named `clean` is ever created in this directory.

- Since it has no prerequisites, `clean` would always be considered up to date and its recipe would not be executed.

- To avoid this problem you can explicitly declare the target to be phony by making it a prerequisite of the special target `.PHONY` as follows:

	- See Section 4.8 Special Built-in Target Names.

	```Makefile
	.PHONY: clean
	clean:
		rm *.o temp
```

- Once this is done, '`make clean`' will run the recipe regardless of whether there is a file named `clean`.

- Prerequisites of `.PHONY` are always interpreted as literal target names, never as patterns.

- Phony targets are also useful in conjunction with recursive invocations of `make`

	- See Section 6.7 Recursive Use of `make`.
