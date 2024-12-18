# json-tools

A few simple scripts wrapping [**_jq_**](https://github.com/jqlang/jq) pipelines
that perform common json-manipulation tasks.

- **json-combine**: combine json files
- **json-env**: generate json from environment vars
- **json-merge**: merge json objects
- **json-wrap**: wrap json in a top-level object

In addition, there is a launcher script **json**
that launches any command on the path that begins with `json-`.

> [!Warning]
>
> These scripts are a work in progress and scnatily tested.
> Use them at your own risk.

I use these scripts. They are also a way for me to learn more about [**_jq_**](https://github.com/jqlang/jq).
I'm incrementally moving the logic from the shell wrappers into the jq pipelines, but
at the moment I'm hesitant to have [**_jq_**](https://github.com/jqlang/jq) do the argument-parsing.

## Installation

The scripts can be found in the `bin` directory; they are ready to execute.
If you put them on your path than the launcher `json` will be able to find them.

The scripts have three dependencies:

- **_zsh_** in which the scripts are written.
- **_jq_** which does all the actual work.
- **_awk_** which generates usage and version text. (Any flavour should do.)

The **json** script isn't actually required—the other scripts can be run directly.
(But who wants to type `json-env` when you can type `json env`? Much more fun.)

## Usage examples

Most of these commands expect json files as arguments. In these examples I'm invoking the commands from the _zsh_ shell using a combination of here strings and process substitution in lieu of files.

These are simple examples. Use the `--help` option for each command to discover all available options.

### json

This is a launcher. Use `json --help` to list the other scripts. Invoke the other scripts as `json combine`, etc.. (That's right, you get to type a space instead of a space!)

### json-combine

`json combine <(<<<'{"foo":"bar"}') <(<<<42)` produces:

```json
{
  "11": {
    "foo": "bar"
  },
  "12": 42
}
```

> [!Note]
>
> Here, `11` and `12` are generated from the file-descriptors. If the data is coming from files their basenames (without any `.json` extension) will be used here.

### json-wrap

This is the similar to `json combine` with a single argument, although it also provides an option to specify the name of the enclosing object. `json wrap --key foo <(<<<'{"bar":"baz"}') <(<<<'"qux"')` produces:

```json
{
  "foo": {
    "bar": "baz"
  }
}
{
  "foo": "qux"
}
```

It's worth noting that this will output an object for each input, printed successively, while the other commands will only print a single top-level object.

### json-merge

`json merge <(<<<'{"foo":"bar"}') <(<<<'{"baz":"qux"}')` produces:

```json
{
  "foo": "bar",
  "baz": "qux"
}
```

### json-env

This one doesn't expect files as arguments, instead it expects patterns to match against environment variable names. On my machine, `json env PAGER 'HOME.*'` produces:

```json
{
  "HOMEBREW_PREFIX": "/opt/homebrew",
  "HOMEBREW_CELLAR": "/opt/homebrew/Cellar",
  "PAGER": "less",
  "HOMEBREW_REPOSITORY": "/opt/homebrew",
  "HOME": "/Users/erik"
}
```
