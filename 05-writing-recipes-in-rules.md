# 5 Writing Recipes in Rules

- The recipe of a rule consists of one or more shell command lines to be executed, one at a time, in the order they appear.

- Recipes in makefiles are always interpreted by `/bin/sh` unless the makefile specifies otherwise.

## 5.5 Errors in Recipes

- After each shell invocation returns, `make` looks at its exit status.

- If the shell completed successfully, the next line in the recipe is executed in a new shell.

- If there is an error, `make` gives up on the current rule, and perhaps on all rules.

- Sometimes the failure of a certain recipe does not indicate a probelm.

- To ignore errors in a recipe line, write a '`-`' at the beginning of the line's text.

- The '`-`' is discarded before the line is passed to the shell for execution.
