---
tags:
  - Systems_Programming
---
### `cut.py`

```python
#!/usr/bin/env python3
''' cut.py - remove sections from each line of stream '''

import io
import sys


# Functions

def usage(exit_status: int=0) -> None:
    ''' Print usage message and exit. '''
    print('''Usage: cut -d DELIMITER -f FIELDS
Print selected parts of lines from stream to standard output.
    -d DELIMITER    Use DELIM instead of TAB for field delimiter
    -f FIELDS       Select only these fields''', file=sys.stderr)
    sys.exit(exit_status)


def strs_to_ints(strings: list[str]) -> list[int]:
    ''' Convert all strings in list to integers.
    >>> strs_to_ints(['2', '4'])
    [2, 4]
    '''
    #implemented the followign (for future reference):
    int_list = []
    length = len(strings)

    for i in range(length):
        value  = int(strings[i])
        int_list.append(value)

    return int_list


def cut_line(line: str, delimiter: str='\t', fields: list[int]=[]) -> list[str]:
    ''' Return selected fields from line separated by delimiter.
    >>> cut_line('Harder, Better, Faster, Stronger', ',', [2, 4])
    [' Better', ' Stronger']
    '''
    #implemented the followign (for future reference):
    tokens = line.rstrip('\n').split(delimiter)
    info = [] # this is the list i am returning

    for i in range(len(fields)):
        index = fields[i] - 1
        if index < len(tokens):
            info.append(tokens[index])

    return info


def cut_stream(stream=sys.stdin, delimiter: str='\t', fields: list[int]=[]) -> None:
    ''' Print selected parts of lines from stream to standard output.
    >>> cut_stream(io.StringIO('Harder, Better, Faster, Stronger'), ',', [2, 4])
       Better, Stronger
    '''
    #implemented the followign (for future reference):
    for line in stream:
        line_info  = cut_line(line, delimiter, fields) #cut line returns a list
        printing_string = delimiter.join(line_info)
        print(printing_string)


# Main Execution

def main(arguments=sys.argv[1:], stream=sys.stdin) -> None:
    ''' Print selected parts of lines from stream to standard output.
    This function will parse the command line arguments to determine the
    delimiter and which fields to select from each line.
    >>> main('-d , -f 2,4'.split(), io.StringIO('Harder, Better, Faster, Stronger'))
       Better, Stronger
    '''
    #implemented the followign (for future reference):
    delimiter = '\t'
    fields = []

    # Parse command line arguments
    if not arguments or '-h' in arguments:
        usage(0)

    for index, arg in enumerate(arguments):
        match arg:
            case '-d': delimiter = arguments[index+1]
            case '-f':  fields = strs_to_ints(arguments[index+1].split(','))

    if not fields:
        usage(1)

    # Cut stream with delimiter and fields
    cut_stream(stream, delimiter, fields)


if __name__ == '__main__':
    main()


# vim: set sts=4 sw=4 ts=8 expandtab ft=python:
```

---

### `wc.py`

```python
#!/usr/bin/env python3

''' wc.py - print newline, word, and byte counts for stream '''

import io
import sys

# Functions

def usage(exit_status: int=0) -> None:
    ''' Print usage message and exit. '''
    print('''Usage: wc.py [-l | -w | -c]

Print newline, word, and byte counts from standard input.

The options below may be used to select which counts are printed, always in
the following order: newline, word, byte.

    -c      Print byte counts
    -l      Print newline counts
    -w      Print word counts''', file=sys.stderr)
    sys.exit(exit_status)

def count_stream(stream=sys.stdin) -> dict[str, int]:
    ''' Count the newlines, words, and bytes in specified stream.

    >>> count_stream(io.StringIO('Despite all my rage, I am still just a rat in a cage'))
    {'newlines': 1, 'words': 13, 'bytes': 52}
    '''
    #Implentted the following (for future referece): 
    text = stream.read()
    content = {}
    content['newlines'] = text.count('\n') #counting all new line chars
    if text and not text.endswith('\n'): #counting non-empty lines that don't end in new line chars
        content['newlines'] += 1
    content['words'] = len(text.split())
    content['bytes'] = len(text.encode())
    return content    

def print_counts(counts: dict[str, int], options: list[str]) -> None:
    ''' Print the newline, word, and byte counts.  If none of the options are
    specified, then include all options in output.  Othewrise, only include the
    specified options.

    Note: always output the counts the following order: newlines, words, bytes.

    >>> print_counts({'newlines': 1, 'words': 13, 'bytes': 52}, ['newlines', 'words', 'bytes'])
     1 13 52
    '''
    #implemented after this(for future reference):
    order = ['newlines', 'words', 'bytes']
    selected = []
    if not options:
        options = order
    for key in order:
        if key in options:
            selected.append(counts[key])
    if len(selected) ==1:
        print(selected[0])
        return

    #getting max width of #'s to be printed 
    width = max(len(str(counts[key])) for key in order)

    #formatting each count using rjust()
    formatted = []
    for count in selected:
        formatted.append(str(count).rjust(width))

    #join with spaces and print
    print(' '.join(formatted))

# Main Execution

def main(arguments=sys.argv[1:], stream=sys.stdin) -> None:
    ''' Print the newline, word, and byte counts from stream.

    This function will parse the command line arguments to select which counts
    to include in the final report.

    >>> main([], io.StringIO('Despite all my rage, I am still just a rat in a cage'))
     1 13 52
    '''
    #implemented the followng (for future reference):
    options = []
    # Parse command line arguments
    while arguments:
        argument = arguments.pop(0)
        match argument:
            case '-c': options.append("bytes")
            case '-l': options.append("newlines")
            case '-w': options.append("words")
            case '-h': usage(0)
            case _ : usage(1) 

    # Count stream and print counts
    text_dict = count_stream(stream)
    print_counts(text_dict, options)

if __name__ == '__main__':
    main()

# vim: set sts=4 sw=4 ts=8 expandtab ft=python:

```