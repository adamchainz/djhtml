# DjHTML

***A pure-Python Django/Jinja2 template indenter without dependencies.***

DjHTML is a fully automatic template indenter that works with mixed
HTML/CSS/Javascript templates that contain
[Django](https://docs.djangoproject.com/en/stable/ref/templates/language/)
or [Jinja2](https://jinja.palletsprojects.com/templates/) template
tags. It works similar to other code-formatting tools such as
[Black](https://github.com/psf/black) and interoperates nicely with
[pre-commit](https://pre-commit.com/).

DjHTML is an _indenter_ and not a _formatter_: it will only add/remove
whitespace at the beginning of lines. It will not insert newlines or
other characters. The goal is to correctly indent already
well-structured templates, not to fix broken ones.

For example, consider the following incorrectly indented template:

```jinja
<!doctype html>
<html>
    <body>
        {% block content %}
        Hello, world!
        {% endblock %}
        <script>
            $(function() {
            console.log('Hi mom!');
            });
        </script>
    </body>
</html>
```

This is what it will look like after processing by DjHTML:

```jinja
<!doctype html>
<html>
    <body>
        {% block content %}
            Hello, world!
        {% endblock %}
        <script>
            $(function() {
                console.log('Hi mom!');
            });
        </script>
    </body>
</html>
```


## Installation

Install DjHTML with the following command:

    $ pip install djhtml


## Usage

After installation you can indent templates using the `djhtml`
command. The default is to write the indented output to standard out.
To modify the source file in-place, use the `-i` / `--in-place`
option:

    $ djhtml -i template.html
    reindented template.html
    All done! \o/
    1 template reindented.

The other available options are:

- `-h` / `--help`: show overview of available options
- `-q` / `--quiet`: don't print any output
- `-t` / `--tabwidth`: set tabwidth (default is 4)
- `-o` / `--output-file`: write output to specified file

The installer also installs the `djtxt`, `djcss`, and `djjs` commands
for indenting plain text, CSS and Javascript source files,
respectively.


## `fmt:off` and `fmt:on`

You can exclude specific lines from being processed with the
`{# fmt:off #}` and `{# fmt:on #}` operators:

```jinja
<div class="
    {# fmt:off #}
      ,-._|\
     /     .\
     \_,--._/
    {# fmt:on #}
/>
```

Contents inside `<pre>`, `/* ... */` and `{% comment %}` tags are also
ignored (depending on the current mode).


## Modes

The indenter operates in one of three different modes:

- DjHTML mode: the default mode. Invoked by using the `djhtml` command
  or the pre-commit hook.

- DjCSS mode. Will be entered when a `<style>` tag is encountered in
  DjHTML mode. It can also be invoked directly with the command
  `djcss`.

- DjJS mode. Will be entered when a `<script>` tag is encountered in
  DjHTML mode. It can also be invoked directly with the command
  `djjs`.

In all modes, Django and Jinja2 template tags are recognized and
indented correctly. Template tags have "indentation precedence" over
other tags, as in the following example:

```jinja
<a>
    <a>
        <a>
            <a>
                <a>
{% for x in [1,2,3,4,5] %}
    </a>
{% endfor %}
```

Without this precedence, the `{% for %}` tag would have been nested 5
levels deep, beneath the `<a>` tag.


## pre-commit configuration

You can use DjHTML as a [pre-commit](https://pre-commit.com/) hook to
automatically indent your templates upon each commit.

First, install pre-commit:

    $ pip install pre-commit
    $ pre-commit install

Then, add the following to your `.pre-commit-config.yaml`:

```yml
repos:
- repo: https://github.com/rtts/djhtml
  rev: main
  hooks:
    - id: djhtml
```

Finally, run the following command:

    $ pre-commit autoupdate

Now when you run `git commit` you will see something like the
following output:

    $ git commit

    djhtml...................................................................Failed
    - hook id: djhtml
    - files were modified by this hook

    reindented template.html
    All done! \o/
    1 template reindented.

To inspect the changes that were made, use `git diff`. If you are
happy with the changes, you can commit them normally. If you are not
happy, please do the following:

1. Run `SKIP=djhtml git commit` to commit anyway, skipping the
   `djhtml` hook.

2. Consider [opening an issue](https://github.com/rtts/djhtml/issues)
   with the relevant part of the input file that was incorrectly
   formatted, and an example of how it should have been formatted.

Your feedback for improving DjHTML is very welcome!

## Development

Use your preferred system for setting up a virtualenv, docker environment,
or whatever else, then run the following:

```sh
python -m pip install -e .[dev]
pre-commit install --install-hooks
```

Tests can then be run quickly in that environment:

```sh
python -m unittest discover -v
```

Or testing in all available supported environments and linting can be run
with [`nox`](https://nox.thea.codes):

```sh
nox
```
