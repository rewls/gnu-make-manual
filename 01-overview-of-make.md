# 1. Overview of `make`

- The `make` utility automatically determines which pieces of a large program need to be recompiled, and issues commands to recompile them.

- This manual describes GNU `make`, which was implemented by Richard Stallman and Roland McGrath.

- Development since Version 3.76 has been handled by Paul D. Smith.

- GNU `make` conforms to section 6.2 of IEEE Standard 1003.2-1992 (POSIX.2).

- To prepare to use `make`, you must write a file called the *makefile* that describes the relationships among files in your program and provides commands for updating each file.

- Once a suitable makefile exists, each time you change some source files, this simple shell command:

```sh
make
```

- suffices to perform all necessary recompilations.

- The `make` program uses the makefile data base and the last-modification times of the files to decide which of the files need to be updated.

- You can provide command line arguments to `make` to control which files should be recompiled, or how.

    - See Chapter 9 [How to Run `make`].

## 1.1. How to Read This Manual

- If you are new to `make`, or are looking for a general introduction, read the first few sections of each chapter, skipping the later sections.

- The exception is Chapter 2 [An Introduction to Makefiles], all of which is introductory.

- If you are familiar with other `make` programs, see Chapter 14 [Features of GNU `make`], which lists the enhancements GNU `make` has, and Chapter 15 [Incompatibilities and Missing Features], which explains the few things GNU `make` lacks that others have.

- For a quick summary, see Section 9.8 [Options Summary], Appendix A [Quick Reference], and Section 4.8 [Special Targets].

## 1.2. Problems and Bugs

- If you have problems with GNU `make` or think you've found a bug, please report it to the developers.
