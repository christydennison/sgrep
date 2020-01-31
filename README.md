# sgrep
`sgrep` is a tool to search code for *s*ymantic patterns. `sgrep` is extensible, supporting single patterns on the command line or multiple from a config file.

## Installation
### Docker
`sgrep` is packaged within a docker container, making installation as easy as [installing docker](https://docs.docker.com/install/).

### CI
```bash
# todo
```

### Mac
```bash
brew install sgrep
```

### Unix
```bash
# todo
```

## Usage
### Basic
The basic `sgrep` command invokes the `sgrep` docker image:
```bash

docker run --rm -v path/to/search:/home/repo returntocorp/sgrep -e '<PATTERN>' -l 'python'

```
Change `path/to/search` to `$(pwd)` to search the code in your current directory. (`/home/repo` is simply the directory your code is copied to within the docker container).

Here, `sgrep` will search all files in the `path/to/search` directory for the pattern `<PATTERN>` and print the results to `stdout`. `sgrep` will parse the pattern and all files in the directory as the specified language (`python` in this example), and will skip the file if parsing fails.

To see more options, view the help screen:
```bash

docker run --rm returntocorp/sgrep --help

```

### Config Files
#### Format
Config files allow you to specify multiple patterns by specifying rules. A rule contains a pattern and information about the pattern. The config file can contain as many rules as you would like. For example, a config file could look like this:
```yml
rules:
  - id: rule_0
    pattern: <PATTERNA>
    message: Pattern A hit
    languages: [python]
    severity: OK
  - id: rule_1
    pattern: <PATTERNB>
    message: Pattern B hit
    languages: [python]
    severity: WARNING
```
Each  `severity` can be `ERROR`, `WARNING`, or `OK`. The `OK` state tells `sgrep` to exit successfully if the pattern is matched, giving you a way to stop searching early if certain conditions are met. Both `WARNING` and `ERROR` cause an unsuccessful exit.

`sgrep` also accepts a config file from a URL or the sgrep registry.

To read more about patterns, see the "Patterns" section below.

#### sgrep Registry
Excellent config files can be time-intensive to write. r2c provides a registry of config files designed by security experts that you can seamlessly use in all of your searches. To browse and search for config files, go to the [sgrep registry](https://sgrep.dev/registry).

### Default
In order to ensure default searches for codebases, you can create a `path/to/search/.sgrep.yml` file or multiple files matching `path/to/search/.sgrep/*.yml` and by default `sgrep` will load those in place of a command line pattern.

## Patterns
Patterns are snippets of code that can be 

#### Metavariables
Metavariables are variables that match text dynamically in a search. For example, the pattern `foo($A, $A)` will match any function call of `foo` where both the first and second argument are the same, like `foo(5, 5)` and `foo(9, 9)`.

#### Operators
Unlike `grep`, `sgrep` does not use `*` as the wildcard operator, as `*` frequently appears in code.

#### Equivalences
`sgrep` automatically searches for code that is semantically equivalent. For example, a pattern for
```python
def foo(a, b, c, d):
```

will match

```python
def foo(a,
    b,
    c,
    d):
```
and other semantically equivalent configurations.

## Outstanding Issues
 * Python `...` slicing operator is not searchable

