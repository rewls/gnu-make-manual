# 8 Functions for Transforming text

- *Functions* allow you to do text processing in the makefile to compute the files to operate on or the commands to use in recipes.

- You use a function in a *function call*, where you give the name of the function and some text (the *arguments*) for the function to operate on.

- The result of the function's processing is substituted into the makefile at the point of the call, just as a variable might be substitute.

## 8.1 Function Call Syntax

- A function call resembles a variable reference.

- It can appear anywhere a variable reference can appear, and it is expanded using the same rules as variable references.

- A function call looks like this:

	```Makefile
	$(*function* *arguments*)
	```

- or like this:

	```Makefile
	${*function* *arguments*}
	```

- The *arguments* are separated from the function name by one or more spaces or tabs, and if there is more than one argument, then they are separated by commas.

- Such whitespace and commas are not part of an argument's value.

- The delimiters which you use to surround the function call, whether parentheses or braces, can appear in an argument only in matching pairs; the other kind of delimiters may appear singly.

- If the arguments themselves contain other function calls or variable references, it is wisest to use the same kind of delimiters for all the references.

- This is because it is clearer, and because only one type of delimiter is matched to find the end of the reference.

- Each argument is expanded before the function is invoked, unless otherwise noted below.

- The substitution is done in the order in which the arguments appear.

### Special Characters

- When using characters that are special to `make` as function arguments, you may need to hide them.

- GNU `make` doesn't support escaping characters with backslashes or other escape sequences; however, because arguments are split before they are expanded you can hide them by putting them into variables.

- Characters you may need to hide include:

	- Commas

	- Initial whitespace in the first argument

	- Unmatched open parenthesis or brace

	- An open parenthesis or brace if you don't want it to start a matched pair

## 8.3 Functions for File Names

- The argument of the function is regarded as a series of file names, separated by whitespae.

- Each file name in the series is transformed in the same way and the results are concatenated with single spaces between them.

- `$(notdir *names*...)`

	- Extracts all but the directory-part of each file name in *names*.
