# Literate

Universal preprocessor for literate programming, using Markdown code blocks (the
same convention used in Literate CoffeeScript), written in (what else?) Literate
CoffeeScript.

## Licence

Copyright &copy; 2015, Ember Group. All rights reserved.

MIT licensed; see `LICENSE`.

## Dependencies

Depends on [Commander.js](https://github.com/tj/commander.js). Use the usual
`npm install -g commander` to get it.

## Setup

		literate = (files..., args) ->

Import some modules.

			fs = require 'fs'

List a bunch of languages by file extension with the compilers that the output
of this program should be piped to if the `-c` option is used.

			compilers =
				'c': if args.use_clang 'clang <> -o ' else 'gcc <> -o ' # C
				'cpp': if args.use_clang 'clang <> -o ' else 'g++ <> -o ' # C++
				'cc': if args.use_clang 'clang <> -o ' else 'g++ <> -o ' # C++
				'cxx': if args.use_clang 'clang <> -o ' else 'g++ <> -o ' # C++
				'm': 'clang <> -o ' # Objective-C
				'java': 'javac <>' # Java

## Parser

Do the following for each file:

			for file in files

Read it in to a string.

Determine the appropriate output extension, and set a flag if it changes. (For
the compilation stage later.)

Create the output file with an appropriate file name.

Separate the input into lines.

Iterate through the lines, doing the following:

If the line begins with three backticks (`\`\`\``), begin or end a fenced block.

If the line begins with four spaces, or we are inside a fenced code block,
keep it (removing the spaces) and output it to the output file. Otherwise,
discard it.

## Compilation (optional)

If the compile flag is set...

Check to make sure all files are in the same language, otherwise exit or return.
