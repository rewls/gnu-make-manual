# 6 How to Use Variables

- A *variable* is a name defined in a makefile to represent a string of text, called the variable's *value*.

- These values are substituted by explicit request into targets, prerequisites, recipes, and other parts of the makefile.

- Variables and functions in all parts of a makefile are expanded when read, except form in recipes, the right-hand sides of variable definitions using '`=`', and the bodies of variable definitions using the `define` directive.

- The value a variable expands to is that of its most recent definition at the time of expansion.

- A variable name may be any sequence of characters not containing '`:`', '`#`', '`=`', or whitespace.

- Variable names beginning with '.' and an uppercase letter may be given special meaning in future versions of `make`.

- Variable names are case-sensitive.

- It is traditional to use upper case letters in variable names, but we recommend using lower case letters for variable names that serve internal purposes in the makefile, and reserving upper case for parameters that control implicit rules or for parameters that the user should override with command options.

- A few variables have names that are a single punctuation character or just a few characters.

- These are the *automatic variables*, and they have particular specialized uses.

	- See Section 10.5.3 [Automatic Variables].

## 6.1 Basics of Variable References

- To substitute a variable's value, write a dollar sign follwed by the name of the variable in parentheses or braces.

- This special significance of '`$`' is why you must write '`$$`' to have the effect of a single dollar sign in a file name or recipe.

- Variable references can be used in any context: targets prerequisites, recipes, most directives, and new variable values.

- Spaces before the variable value are ignored in variable assignments.

- A dollar sign followed by a character other than a dollar sign, open-parenthesis or open-brace treats that single character as the variable name.

- We recommend using parentheses or braces around all variables, even single-letter variables, unless omitting them gives significant readability improvements.

- One place where readability is often improved is automatic variables.

	- See Section 10.5.3 [Automatic Variables].

## 6.2 The Two Flavors of Variables

- There are different ways that a variable in GNU `make` can get a value; we call them the *flavors* of variables.

- The flavors are distinguished in how they handle the values they are assigned in the makefile, and in how those values are managed when the variable is later used and expanded.

### 6.2.1 Recursively Expanded Variable Assignment

- The first flavor of variabl is a *recursively expanded* variable.

- Variables of this sort are defined by lines using '`=`' or by the `define` directive.

	- See Section 6.5 [Setting Variables]

	- See Section 6.8 [Defining Multi-Line Variables].

- The value you specify is installed verbatim; if it contains references to other variables, these references are expanded whenever this variable is substituted.

- This flavor of variable is the only sort supported by most other versions of `make`.

- It has its advantages and its disadvantages.

- An advantage is that:

	```Makefile
	CFLAGS = $(include_dirs) -O
	include_dirs = -Ifoo -Ibar
	```

- will do what was intended.

- A major disadvantage is that you cannot append something on the end of a variable.

- Another disadvantage is that any functions referenced in the definition will be executed every time the variable is expanded.

	- Chapter 8 [Functions for Transforming Text].

- This makes `make` run slower; worse, it causes the `wildcard` and `shell` functions to give unpredictable results because you cannot easily control when they are called, or  even how many times.

### 6.2.2 Simply Expanded Variable Assignment

- To avoid the problems and inconveniences of recursively expanded variables, there is another flavor: simply expanded variables.

- *Simply expanded variables* are defined by lines using '`:=`' or '`::=`'.

	- See Section 6.5 [Setting Variables].

- Both forms are equivalent in GNU `make`; however only the '`::=`' form is described by the POSIX standard.

- The value of a simply expanded variable is scanned once, expanding any references to other variables and functions, when the variable is defined.

- If the value contained variable references the result of the expansion will contain their value *as of the time this variable was defined*.

- You can also use them to introduce controlled leading whitespace into variable values.

- Leading whitespace characters are discarded from your input before substitution of variable references and function calls; this means you can include leading spaces in a variable value by protecting them with variable references, like this:

	```Makefile
	nullstring :=
	space := $(nullstring) # end of the line
	```

- Here the value of the variable `space` is precisely one space. 

## 6.3 Advanced Features for Reference to Variables

### 6.3.1 Substitution References

- A *substitution reference* substitues the value of a variable with alterations that you specify.

- It has the form '`$(*var*:*a*=*b*)`' (or '`${*var*:*a*=*b*}`') and its meaning is to take the value of the variable *var*, replace every *a* at the end of a word with *b* in that value, and substitute the resulting string.

- When we say "at the end of a word", we mean that *a* must appear either followed by whitespace or at the end of the value in order to be replaced; other occurrences of *a* in the value are unaltered.

- For example:

	```Makefile
	foo := a.o b.o l.a c.o
	bar := $(foo:.o=.c)
	```

	- See Section 6.5 [Setting Variables].

- A substitution reference is shorthand for the `patsubst` expansion function: '`$(*var*:*a*=*b*)`' is equivalent to '`$(patsubst %*a*,%*b*,*var*)`'.

	- See Section 8.2 [Functions for String Substitution and Analysis].

## 6.6 Appending More Text to Variables

- You add more text to the value of a variable already defined with a line containing '`+=`'.

- When the variable in question has not been defined before, "`+=`' acts just like normal '='.

- However, when there *is* a previous definition, exactly what '`+=`' does depends on what flavor of variable you defined originally.
