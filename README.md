# NAME

Perl::Critic::Policy::ProhibitUnusedImports - An import nobody uses is a dependency nobody knew they had.

# VERSION

version 1.000

# Perl::Critic::Policy::ProhibitUnusedImports

A `use` nobody uses costs load time, drags a prerequisite along behind it, and
tells the next reader something about this file that isn't true:

```perl
use FindBin();          # ...and $FindBin::Bin appears nowhere below
use POSIX qw{floor};    # ...and floor() is never called
```

They accumulate. A sub gets rewritten, the import that fed it stays, and a year
later somebody is keeping a module in `Makefile.PL` for the sake of a line that
does nothing.

This resolves each imported name and looks for a use of it in the rest of the
document:

- `use Module LIST`

    Each name in the list has to appear -- as a call, a symbol, or after an `&`.

- `use Module;`

    Whatever `@EXPORT` says, if the module can be loaded and says anything.  With
    an empty `@EXPORT` this is the same case as the next one.

- `use Module ();`

    Nothing is imported, so the module's own name has to appear: `Module::thing`,
    `Module->method`, or `$Module::VAR`.

## PROHIBITED

```perl
use FindBin();
print "hello\n";                    # nothing named FindBin below

use List::Util qw{first any};
my @x = grep { $_ } @y;             # neither one called
```

## ALLOWED

```perl
use FindBin();
print $FindBin::Bin;

use List::Util qw{any};
return any { $_ } @list;
```

Pragmas and other modules whose entire job is a side effect are exempt; see
`allow` below.

## CONFIGURATION

- `allow`

    Space separated list of modules that are never reported, because importing them
    _is_ the point. Defaults to the pragmas plus the usual side-effect modules:

    ```perl
    strict warnings utf8 feature lib FindBin::libs parent base constant
    vars subs overload open integer bytes locale sigtrap version experimental
    Filter::Simple Carp::Always

    [ProhibitUnusedImports]
    allow = strict warnings My::Company::Bootstrap
    ```

## CAVEATS

Modules exporting into a namespace this file only reaches at runtime -- a
symbolic call, a string `eval`, an `AUTOLOAD` -- read as unused, because the
source does not say otherwise. Add them to `allow`.

Loading the module to read its `@EXPORT` means running its top-level code, so
this policy is unsafe by Perl::Critic's definition and needs
`--allow-unsafe`. When the module will not load, its default exports are
unknown and the `use Module;` case is skipped rather than guessed at.

## METHODS

### supported\_parameters

`allow`, the modules that are never reported.

### default\_severity

SEVERITY\_LOW

### default\_themes

maintenance, performance

### applies\_to

PPI::Statement::Include

### is\_safe

False. Reading `@EXPORT` means loading the module, which runs its top-level
code, so this policy needs `--allow-unsafe`.

### violates

Standard [Perl::Critic::Policy](https://metacpan.org/pod/Perl%3A%3ACritic%3A%3APolicy) interface. Returns a violation for a `use`
whose imports -- or whose own name, when it imports nothing -- appear nowhere
else in the document.

# BUGS

Please report any bugs or feature requests on the bugtracker website
[https://github.com/teodesian/perl-critic-policy-prohibitunusedimports/issues](https://github.com/teodesian/perl-critic-policy-prohibitunusedimports/issues)

When submitting a bug or request, please include a test-file or a
patch to an existing test-file that illustrates the bug or desired
feature.

# AUTHORS

Current Maintainers:

- George S. Baugh <teodesian@gmail.com>

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
