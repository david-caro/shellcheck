% SHELLCHECK(1) Shell script analysis tool

# NAME

shellcheck - Shell script analysis tool

# SYNOPSIS

**shellcheck** [*OPTIONS*...] *FILES*...

# DESCRIPTION

ShellCheck is a static analysis and linting tool for sh/bash scripts. It's
mainly focused on handling typical beginner and intermediate level syntax
errors and pitfalls where the shell just gives a cryptic error message or
strange behavior, but it also reports on a few more advanced issues where
corner cases can cause delayed failures.

ShellCheck gives shell specific advice. Consider the line:

    (( area = 3.14*r*r ))

+ For scripts starting with `#!/bin/sh` (or when using `-s sh`), ShellCheck
will warn that `(( .. ))` is not POSIX compliant (similar to checkbashisms).

+ For scripts starting with `#!/bin/bash` (or using `-s bash`), ShellCheck
will warn that decimals are not supported.

+ For scripts starting with `#!/bin/ksh` (or using `-s ksh`), ShellCheck will
not warn at all, as `ksh` supports decimals in arithmetic contexts.


# OPTIONS

**-e**\ *CODE1*[,*CODE2*...],\ **--exclude=***CODE1*[,*CODE2*...]

:   Explicitly exclude the specified codes from the report. Subsequent **-e**
    options are cumulative, but all the codes can be specified at once,
    comma-separated as a single argument.

**-f** *FORMAT*, **--format=***FORMAT*

:   Specify the output format of shellcheck, which prints its results in the
    standard output. Subsequent **-f** options are ignored, see **FORMATS**
    below for more information.

**-s**\ *shell*,\ **--shell=***shell*

:   Specify Bourne shell dialect. Valid values are *sh*, *bash*, *dash* and *ksh*.
    The default is to use the file's shebang, or *bash* if the target shell
    can't be determined.

**-V**,\ **--version**

:   Print version information and exit.

**-x**,\ **-external-sources**

:   Follow 'source' statements even when the file is not specified as input.
    By default, `shellcheck` will only follow files specified on the command
    line (plus `/dev/null`). This option allows following any file the script
    may `source`.

# FORMATS

**tty**

:   Plain text, human readable output. This is the default.

**gcc**

:   GCC compatible output. Useful for editors that support compiling and
    showing syntax errors.

    For example, in Vim, `:set makeprg=shellcheck\ -f\ gcc\ %` will allow
    using `:make` to check the script, and `:cnext` to jump to the next error.

        <file>:<line>:<column>: <type>: <message>

**checkstyle**

:   Checkstyle compatible XML output. Supported directly or through plugins
    by many IDEs and build monitoring systems.

        <?xml version='1.0' encoding='UTF-8'?>
        <checkstyle version='4.3'>
          <file name='file'>
            <error
              line='line'
              column='column'
              severity='severity'
              message='message'
              source='ShellCheck.SC####' />
            ...
          </file>
          ...
        </checkstyle>

**json**

:   Json is a popular serialization format that is more suitable for web
    applications. ShellCheck's json is compact and contains only the bare
    minimum.

        [
          {
            "file": "filename",
            "line": lineNumber,
            "column": columnNumber,
            "level": "severitylevel",
            "code": errorCode,
            "message": "warning message"
          },
          ...
        ]

# DIRECTIVES
ShellCheck directives can be specified as comments in the shell script
before a command or block:

    # shellcheck key=value key=value
    command-or-structure

For example, to suppress SC2035 about using `./*.jpg`:

    # shellcheck disable=SC2035
    echo "Files: " *.jpg

To tell ShellCheck where to look for an otherwise dynamically determined file:

    # shellcheck source=./lib.sh
    source "$(find_install_dir)/lib.sh"

Here a shell brace group is used to suppress a warning on multiple lines:

    # shellcheck disable=SC2016
    {
      echo 'Modifying $PATH'
      echo 'PATH=foo:$PATH' >> ~/.bashrc
    }

Valid keys are:

**disable**
:   Disables a comma separated list of error codes for the following command.
    The command can be a simple command like `echo foo`, or a compound command
    like a function definition, subshell block or loop.

**source**
:   Overrides the filename included by a `source`/`.` statement. This can be
    used to tell shellcheck where to look for a file whose name is determined
    at runtime, or to skip a source by telling it to use `/dev/null`.

# ENVIRONMENT VARIABLES
The environment variable `SHELLCHECK_OPTS` can be set with default flags:

    export SHELLCHECK_OPTS='--shell=bash --exclude=SC2016'

Its value will be split on spaces and prepended to the command line on each
invocation.

# RETURN VALUES

ShellCheck uses the follow exit codes:

+ 0: All files successfully scanned with no issues.
+ 1: All files successfully scanned with some issues.
+ 2: Some files could not be processed (e.g. file not found).
+ 3: ShellCheck was invoked with bad syntax (e.g. unknown flag).
+ 4: ShellCheck was invoked with bad options (e.g. unknown formatter).

# AUTHOR
ShellCheck is written and maintained by Vidar Holen.

# REPORTING BUGS
Bugs and issues can be reported on GitHub:

https://github.com/koalaman/shellcheck/issues

# COPYRIGHT
Copyright 2012-2015, Vidar Holen.
Licensed under the GNU General Public License version 3 or later,
see http://gnu.org/licenses/gpl.html


# SEE ALSO

sh(1) bash(1)
