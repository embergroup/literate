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
      path = require 'path'
      child = require 'child_process'

List a bunch of languages by file extension with the compilers that the output
of this program should be piped to if the `-c` option is used.

      CC = if args.use_clang then 'clang' else 'gcc'
      compilers =
        'c': {cmd: CC, preargs: '', postargs: '-o'}
        'cpp': {cmd: CC, preargs: '', postargs: '-o'}
        'cc': {cmd: CC, preargs: '', postargs: '-o'}
        'cxx': {cmd: CC, preargs: '', postargs: '-o'}
        'm': {cmd: 'clang', preargs: '', postargs: '-o'}
        'java': {cmd: 'javac', preargs: '', postargs: ''}

## Parser

Do the following for each file:

      multilingual = false
      old_out_ext = ''
      console.log files
      outputs = for filename in files

Read it in to a string.

        data = fs.readFileSync filename, {encoding: 'utf8'}

Separate the input into lines.

        lines = data.split '\n'

Iterate through the lines, doing the following:

        inFencedBlock = false
        output = ''
        for line in lines

If the line begins with three backticks (\`\`\`), begin or end a fenced block.

          if line.slice 0, 3 is '```'
            inFencedBlock = !inFencedBlock
            continue

If the line begins with four spaces or a tab, or we are inside a fenced code block,
keep it (removing the spaces) and output it to the output file. Otherwise,
discard it.

          else if inFencedBlock
            output += line + '\n'
          else if line.slice(0, 1) is '\u0009' # tab
            output += line.slice(1) + '\n'
          else if line.slice(0, 4) is '    ' # four spaces
            output += line.slice(4) + '\n'

Determine the appropriate output extension, and set a flag if it changes. (For
the compilation stage later.)

        [name, exts...] = path.basename(filename).split '.'
        out_ext = if exts[exts.length-1] is 'md' then exts[exts.length-2]
        else if exts[exts.length-2].slice 0, 3 is 'lit'
          exts[exts.length-1].slice 3
        else exts[exts.length-1]
        output_name = path.dirname(filename) + path.sep + name + '.' + out_ext
        if (old_out_ext != '') and (out_ext != old_out_ext)
          multilingual = true
        old_out_ext = out_ext

Write the output to the output file and tell the loop we're done

        fs.writeFileSync output_name, output

Append the output file name to the list (CoffeeScript does this).

      output_name

## Compilation (optional)

If the compile flag is set...

Check to make sure all files are in the same language, otherwise return.

Call the appropriate command, passing it this process's stdin and stdout.

The End.

      return undefined

    module.exports = literate
