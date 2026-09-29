# NAME

Perl::Critic::Policy::RegularExpressions::ProhibitRegexToChomp - Take a trailing newline off with chomp, not with a substitution.

# VERSION

version 0.001

# Perl::Critic::Policy::RegularExpressions::ProhibitRegexToChomp

A substitution that deletes a newline at the end of a string is `chomp`.
`chomp` says what it does in one word, and it does it without compiling a
pattern and running it:

```perl
$line =~ s/\n\z//;                     # reported
chomp $line;

my $message = $p{message} =~ s/\n+\z//r;   # reported
chomp( my $message = $p{message} );
```

## PROHIBITED

```
s/\n\z//                # \z, \Z and $ all count as the end
s/\n$//r                # /r: chomp a copy instead
s/\n+\z//               # a quantified newline, see CAVEATS
s/\n?\z//               # ? is what chomp does anyway
s/[\n]*\Z//             # a class of one newline is a newline
s/\x0a\z//              # any spelling of the character
s/ \n \z //x            # whitespace under /x is not text
```

## ALLOWED

```
s/\r?\n\z//             # chomp leaves the carriage return
s/\s+\z//               # trailing whitespace, not a newline
s/\n//                  # the first newline, wherever it is
s/\n$//m                # /m: $ is the end of a line
s/\n\z/;/               # a replacement
s/\n{2,}\z//            # a counted quantifier
s/$eol\z//              # interpolation
```

## CAVEATS

`\n+` and `\n*` take off every newline at the end, and `chomp` takes off one.
Text that genuinely ends in more than one newline, and wants all of them gone,
is rare enough that the policy reports it anyway.  When it is the point, keep
the regex and say `## no critic (ProhibitRegexToChomp)` and why.

`chomp` takes off `$/`, which is `"\n"` unless the code around it changed
it.  After `local $/;`, to read a whole file, `chomp` takes off nothing.

`chomp` changes its argument and returns how many characters it removed, not
the string.  Where the substitution had `/r`, copy first:
`chomp( my $copy = $original )`.

A modifier turned on by `use re` is not seen, only one written on the
substitution.  So under a global `/m`, `s/\n$//` is reported although `$`
means the end of a line there.

## METHODS

### supported\_parameters

### default\_severity

### default\_themes

### applies\_to

### violates

# BUGS

Please report any bugs or feature requests on the bugtracker website
[https://github.com/teodesian/perl-critic-policy-prohibitregextochomp/issues](https://github.com/teodesian/perl-critic-policy-prohibitregextochomp/issues)

When submitting a bug or request, please include a test-file or a
patch to an existing test-file that illustrates the bug or desired
feature.

# AUTHORS

Current Maintainers:

- George S. Baugh <george@troglodyne.net>

# COPYRIGHT AND LICENSE

Copyright (c) 2026 Troglodyne LLC

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
