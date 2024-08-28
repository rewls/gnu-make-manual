# 11 Using `make` to Update Archive Files

- *Archive files* are files containing named sub-files called *members*; they are maintained with the program `ar` and their main use is as subroutine libraries for linking.

## 11.1 Archive Members as Targets

- An individual member of an archive file can be used as a target or prerequisite in `make`.

- You specify the member named *member* in archive file *archive* as follows:

    ```Makefile
    *archive*(*member*)
    ```

- This construct is available only in targets and prerequisites, not in recipes!

- Only `ar` and other programs specifically designed to operate on archives can do so.

- For example, this rule says to create a member `hack.o` in archive `foolib` by copying the file `hack.o`:

    ```Makefile
    foolib(hack.o): hack.o
        ar cr foolib hack.o
    ```

- In fact, nearly all archive member targets are updated in just this way and there is an implicit rule to do it for you.

- **Please note**: The '`c`' flag to `ar` is required if the archive file does not already exist.

- To specify several members in the same archive, you can write all the member names together between the parentheses.

- You can also use shell-style wildcards in an archive member reference.

## 11.2 Implicit Rule for Archive Member Targets

- When `make` looks for an implicit rule for `*a*(*m*)`, as a special feature it considers implicit rules that match `(*m*)`, as well as those that match the actual target `*a*(*m*)`.

- This causes one special rule whose target is `(%)` to match.

- This rule updates the target `*a*(*m*)` by copying the file `*m*` into the archive.

- When this rule is chained with others, the result is very powerful.

- Thus, '`make "foo.a(bar.o)"`' in the presence of a file `bar.c` is enough to cause the following recipe to be run, even without a makefile:

    ```Makefile
    cc -c bar.c -o bar.o
    ar r foo.a bar.o
    rm -f bar.o
    ```

- Here `make` has envisioned the file `bar.o` as an intermediate file.

    - See Section 10.4 [Chains of Implicit Rules].

- Implicit rules such as this one are written using the automatic variable '`$%`'.

- An archive member name in an archive cannot contain a directory name, but it may be useful in a makefile to pretend that it does.

- If you write an archive member target `foo.a(dir/file.o)`, `make` will perform automatic updating with this recipe:

    ```Makefile
    ar r foo.a dir/file.o
    ```

- Which has the effect of copying the file `dir/file.o` into a member named `file.o`.

- In connection with such usage, the automatic variable `%D` and `%F` may be useful.

### 11.2.1 Updating Archive Symbol Directories

- An archive file that is used as a library usually contains a special member named `__.SYMDEF` that contains a directory of the external symbol names defined by all the other members.

- After you update any other members, you need to update `__.SYMDEF` so that it will summarize the other members properly.

- This is done by running the `ranlib` program:

    ```Makefile
    ranlib *arcihvefile*
    ```

- Normally you would put this command in the rule for archive file, and make all the members of the archive file prerequisites of that rule.

- For example,

    ```Makefile
    libfoo.a: libfoo.a(x.o y.o ...)
        randlib libfoo.a
    ```

- This is not necessary when using the GNU `ar` program, which updates the `__.SYMDEF` member automatically.

## 11.3 Dangers When Using Archives

- The built-in rules for updating archives are incompatible with parallel builds.

- These rules add each object file into the archive as its compiled.

- When parallel builds are enabled this allows multiple `ar` commands to be updating the same archive simultaneously, which is not supported.
