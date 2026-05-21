---
tags:
  - Systems_Programming
---
### `searx.py`

```python
#!/usr/bin/env python3
''' searx.py - SearX from the command line '''

import sys
import requests

# Constants
URL     = 'https://searx.ndlug.org/search'
LIMIT   = 5
ORDERBY = 'score'

# Functions
def usage(exit_status: int=0) -> None:
    ''' Print usage message and exit. '''
    print(f'''Usage: searx.py [-u URL -n LIMIT -o ORDERBY] QUERY

Fetch SearX results for QUERY and print them out.

    -u URL      Use URL as the SearX instance (default is: {URL})
    -n LIMIT    Only display up to LIMIT results (default is: {LIMIT})
    -o ORDERBY  Sort the search results by ORDERBY (default is: {ORDERBY})

If ORDERBY is score, the results are shown in descending order.  Otherwise,
results are shown in ascending order.''', file=sys.stderr)
    sys.exit(exit_status)

def searx_query(query: str, url: str=URL) -> list[dict]:
    ''' Returns lists of results for query from SearX.

    >>> searx_query('Python', 'https://yld.me/ipfM') # doctest: +ELLIPSIS
    [{'url': 'https://www.python.org/', 'title': 'Welcome to Python.org', ...}]
    '''
    response = requests.get(url, params={'q': query, 'format': 'json'})
    text = response.json()
    return text['results']

def print_results(results: list[dict], limit: int=LIMIT, orderby: str=ORDERBY) -> None:
    ''' Print results of SearX query.

    >>> print_results(searx_query('Python', 'https://yld.me/ipfM')) # doctest: +ELLIPSIS, +NORMALIZE_WHITESPACE
        1.  Welcome to Python.org [...]
            https://www.python.org/
    ...
    '''
    reverse_flag = orderby == 'score'
    sorted_results = sorted(results, key=lambda r: r[orderby], reverse=reverse_flag)
    final_results = sorted_results[:limit]
    n = len(final_results)

    for index, result in enumerate(final_results):
        print(f"{index+1:>4}.\t{result['title']} [{result['score']:0.2f}]")
        print(f"\t{result['url']}")
        if index != (n-1):
            print()

# Main Execution
def main(arguments=sys.argv[1:]) -> None:
    ''' Searches SearX and print results.

    >>> main('-u https://yld.me/ipfM Python'.split()) # doctest: +ELLIPSIS, +NORMALIZE_WHITESPACE
        1.  Welcome to Python.org [...]
            https://www.python.org/
    ...
    '''
    url = URL
    orderby = ORDERBY
    limit = LIMIT

    while arguments and arguments[0].startswith('-'):
        arg = arguments.pop(0)
        match arg:
            case '-u': url = arguments.pop(0)
            case '-n': limit = int(arguments.pop(0))
            case '-o': orderby = arguments.pop(0)
            case '-h': usage(0)
            case _ : usage(1)

    query = ' '.join(arguments)
    if not query:
        return usage(1)

    results = searx_query(query, url)
    print_results(results, limit, orderby)

if __name__ == '__main__':
    main()

# vim: set sts=4 sw=4 ts=8 expandtab ft=python:
```

---

### `hulk.py`

```python

#!/usr/bin/env python3
from typing import Iterable, Iterator
import concurrent.futures
import hashlib
import os
import string
import sys

# Constants
ALPHABET = string.ascii_lowercase + string.digits

# Functions
def usage(exit_code: int=0):
    print('''Usage: hulk.py [-a ALPHABET -c CORES -l LENGTH -p PATH -s HASHES]
    -a ALPHABET Alphabet to use in permutations
    -c CORES    CPU Cores to use
    -l LENGTH   Length of permutations
    -p PREFIX   Prefix for all permutations
    -s HASHES   Path of hashes file''', file=sys.stderr)
    sys.exit(exit_code)

def sha1sum(s: str) -> str:
    ''' Compute SHA1 digest for given string.

    >>> sha1sum('a')
    '86f7e437faa5a7fce15d1ddcb9eaeaea377667b8'
    '''
    # TODO: Use the hashlib library to produce the SHA1 hex digest of the given
    # string.
    return hashlib.sha1(s.encode('utf-8')).hexdigest()

def permutations(length: int, alphabet: str=ALPHABET) -> Iterator[str]:
    ''' Recursively yield all permutations of alphabet up to given length.

    >>> for p in permutations(2, 'ab'): print(p)
    aa
    ab
    ba
    bb
    '''
    # TODO: Use yield to create a generator function that recursively produces
    # all the permutations of the given alphabet up to the provided length.
    if length == 0:
        yield ''
    else:
        for letter in alphabet:
            for perm in permutations(length-1,alphabet):
                yield letter + perm

def flatten(sequence: Iterable[Iterable[str]]) -> Iterator[str]:
    ''' Flatten sequence of iterables.

    >>> for p in flatten([['a', 'b'], ['c', 'd']]): print(p)
    a
    b
    c
    d
    '''
    # TODO: Iterate through sequence and yield from each iterator in sequence.
    for subseq in sequence:
        yield from subseq

def crack(hashes: set[str], length: int, alphabet: str=ALPHABET, prefix: str='') -> list[str]:
    ''' Return all password permutations of specified length that are in hashes
    by trying all possible permutations sequentially.

    >>> for p in crack({sha1sum(l) for l in 'abc'}, 1, 'abcd'): print(p)
    a
    b
    c
    '''
    # TODO: Return list comprehension that iterates over a sequence of
    # candidate permutations and checks if the sha1sum of each candidate is in
    # hashes.
    return [prefix + perm for perm in permutations(length, alphabet) if sha1sum(prefix + perm) in hashes]

def whack(arguments: tuple[set[str], int, str, str]) -> list[str]:
    ''' Call the crack function with the specified list of arguments

    >>> for p in whack([{sha1sum(l) for l in 'abc'}, 1, 'abcd', '']): print(p)
    a
    b
    c
    '''
    return crack(*arguments)

def smash(hashes: set[str], length: int, alphabet: str=ALPHABET, prefix: str='', cores: int=1) -> Iterator[str]:
    ''' Return all password permutations of specified length that are in hashes
    by cracking subsets of all possible permutations concurrently.

    >>> for p in smash({sha1sum(l) for l in 'abc'}, 1, 'abcd'): print(p)
    a
    b
    c
    '''
    # TODO: Create generator expression with arguments to pass to whack and
    # then use ProcessPoolExecutor to apply whack to all items in expression.
    if length == 1 or cores == 1:
        yield from crack(hashes, length, alphabet, prefix)
    else:
        arguments = ((hashes, length-1, alphabet, prefix + letter) for letter in alphabet)
        with concurrent.futures.ProcessPoolExecutor(cores) as executor:
            for sublist in executor.map(whack, arguments):
                for password in sorted(sublist):
                    yield password

# Main Execution
def main(arguments: list[str]=sys.argv[1:]) -> None:
    ''' Smashes given hashes to determine passwords with specified alphabet,
    length, and prefix.  Uses multiple cores (ie. processes) if specified.

    >>> main('-a abcdefg -l 2'.split())
    cg
    fg
    gg
    '''
    alphabet    = ALPHABET
    cores       = 1
    hashes_path = 'hulk.hashes'
    length      = 1
    prefix      = ''

    # TODO: Parse command line arguments
    while arguments and arguments[0].startswith('-'):
        arg = arguments.pop(0)
        match arg:
            case '-a': alphabet = arguments.pop(0)
            case '-c': cores = int(arguments.pop(0))
            case '-l': length = int(arguments.pop(0))
            case '-p': prefix = arguments.pop(0)
            case '-s': hashes_path = arguments.pop(0)
            case '-h': usage(0)
            case _: usage(1)

    # TODO: Load hashes set
    with open(hashes_path, 'r') as f:
        hashes = {line.strip() for line in f}

    # TODO: Execute smash function
    for password in sorted(smash(hashes, length, alphabet, prefix, cores)):
        # TODO: Print all found passwords
        print(password)

if __name__ == '__main__':
    main()

# vim: set sts=4 sw=4 ts=8 expandtab ft=python:
```