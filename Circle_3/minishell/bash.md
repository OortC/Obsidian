 1. Introduction
	 1. bash is the shell, or command language interpreter, for the GNU operating system.
	 2. shell is simply a macro processor that excutes commands. The term macro processor means functionality where text and symbols are expanded to create larger expressions.
2. Definitions
	1. Posix : A family of open system standards based on UNIX. 
	2. blank : A space of tab characther.
	3. builtin A command that is implemented internally by the shell itself, rather than by and executable program somewhere in the file system.
	4. control operator : A `token` that performs a control function. It is a `newline` or one of the following: `||, &&, &, ;, ;;, ;&, ;;&, |, |&, (, )`
	5. exit status : The value returned by a command to tis caller. The value is restricted to eight bits, so the maximum value is 255.
	6. field : A unit of text that is the result of one of the shell expansions. After expansion, when excuting a command, the resulting fields are used as the command name and arguments.
	7. filename :  A string of characters used to identify a file.
	8. job : A set of processes comprising a pipeline, and nay processes descended from it, that are all in the same process group.
	9. job control : A mechanism by which users can selectively stop and restart execution of processes.
	10. matacharacter : A character that, when unquoted, separates words. A metacharacter is a `space, tab, newline` or one of the following charcters: `|, &, ;, (, ), <,  >`
	11. name : A `word` consisting solely of letters, numbers, and underscores, and beggining with a letter or underscore. `names`used as shell variable and function names. Also referred to as an `identifier`
	12. operator : A `control operator` or `redirection operator`. Operators contain at least one unquoted `metacharacter`
	13. process group : A collection of related processes each having the same process group ID
	14. process group ID : A unique identifier that represents a `precess group` during its lifetime.
	15. reserved word : A `word` that has a special meaning to the shell. Most reserved words intorduce shell flow control constructs, such as `for` and `while`
	16. return status : A synonym for `exit status`
	17. signal : A mechanism by which a process may be notified by the kernel of an event occurring in the system.
	18. special builtin : A shell builtin command that has been classifield as special by the POSIX standard.
	19. token : A sequence of characters considered a single unit by the shell. It is either a `word` or an `operator`.
	20. word : A sequence of characters treated as a unit by the shell. Words may not incldue unquoted `metacharacters`
3.  Basic Shell Features
	1. Shell Operation
		1. Reads its input from a file, from a string supplied as an argument to the `-c` invocation option, or from the user's terminal.
		2. Breaks the input into words and operators, obeying the quoting rules. These tokens are separted by `metacharacters`. Alias expansion is performed by thi step.
		3. Parses the tokens into simple and compound commans.
		4. Performs the various shell expansions, breaking the expanded tokens into lists of filenames and command and arguments.
		5. Performs any necessary redirections and removes the redirection operators and their operands from the argument list.
		6. Excutes the command
		7. Optionally waits for the command to complete and collects its exit stauts.
	2. Quoting
		-  Quoting is used to remove the special meaning of certain charaters or words to the shell. Quoting can be used disable special treatment for special characters, to prevent reserved words from being recognized as such, and to prevent parameter expansion.
		1. Escape Character
			- A non-quoted bashslash `\` is the Bash escape character. It preserves the literal value of the next character that follows, with the exception of `newline`. If a `\newline` pair appears, and the backslash itself is not quoted, the `\newline` is treated as a line continuation
		2. Single Quote
			- Enclosing characters in single quotes `'` preserves the literal value of each character within the quotes. A single quote may not occur between single quotes, even when preceded by a bachslash.
		3. Double Quotes
			- Enclosing characters in double quotes `"` preserves the literal value of all characters within the quotes, with the exception of 