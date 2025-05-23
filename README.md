# Creating Python scripts and packages with `uv`

In this session,
we will learn how to create Python scripts and packages with [`uv`]:
an exciting new Python package installer and manager.

Historically, the Python package management landscape has been quite fragmented,
partially because Python is a 30+ old language.
The goal of `uv` is to create a unified toolchain
for installing and packaging Python packages
(including as a stand-in replacement for `pip`).
Even though `uv` was first released in February 2024,
it has been rapidly adopted by people and projects across the community.

The creators of `uv` have also worked hard to grow psychological safety
in the `uv` community.

Here we will go through a few of the most useful parts of `uv`.

## Installing `uv`

These [instructions](https://docs.astral.sh/uv/getting-started/installation/)
describe how to install `uv`.
To test that `uv` is installed correctly, open a terminal and type `uv version`.
`uv` can be used in Unix-style terminals as well as Powershell.

## Creating and managing Python environments

When using Python, we frequently install packages like Astropy
using a command like `pip install astropy`.
By default, this command will install Astropy into the default Python installation.
This typically works well enough most of the time,
but sometimes we'll run into a _package conflict_.

Suppose PlasmaPy version `2024.10.0` requires `astropy < 7.0.0`,
but SunPy `6.1.1` requires `astropy >= 7.0.0`.
These versions of PlasmaPy and SunPy
cannot be installed simultaneously!
The way to get around this is to use virtual environments.

A **virtual environment** is
"an isolated space where you can work on your Python projects,
separately from your system-installed Python."
To resolve the above problem, we can create a virtual environment
to work with SunPy, and a separate virtual environment to work with PlasmaPy.

To keep things organized, let's create a temporary directory to work in.

```bash
mkdir crane_osrse
cd crane_osrse
```

> [!TIP]
> In Unix terminals, the `which` command tells us
> where different executable files are located.

Let's use `which` to see which installation of `python` we are currently using.

```bash
which python
```

Let's [create a virtual environment](https://docs.astral.sh/uv/pip/environments)!

```bash
uv venv --python 3.13
```

Specifying the version of Python with `--python 3.13` isn't necessary,
but ensures that we're using the most recent version of Python
until the next one is released in October 2025.

Let's use `ls` to list the contents of the directory.
We need to use the `-A` flag to show everything,
since files and directories starting with a dot are hidden by default.

```bash
ls -A
```

> [!TIP]
> In PowerShell, use `dir` instead of `ls`.

There is a new directory called `.venv/` which contains the virtual environment.
Let's check which `python` executable we're using:

```bash
which python
```

This is still the same `python` as before because
we still need to _activate_ the virtual environment.
Fortunately, the output of `uv venv` provides the command
to activate the virtual environment.
For the [`bash`] shell in Unix, the command is:

```bash
source .venv/bin/activate
```

> [!NOTE]
> In PowerShell, it may be necessary to run the script as an administrator.

Now let's see which `python` we are using:

```bash
which python
```

Because we activated the environment, the `python` we are using is
located at `.venv/bin/python` relative to the current directory.

> [!IMPORTANT]
> The command to activate a virtual environment must be run
> every time we open a terminal.

> [!TIP]
> To avoid having to manually activate our default environment,
> we can include the command in the configuration file
> for the shell we are using (i.e., `.bashrc` for `bash` and
> `.zshrc` for `zsh` in the home directory).
> I'm happy to help with this after!

To install a package into the current virtual environment,
use `uv pip`:

```bash
uv pip install plasmapy
```

> [!NOTE]
> Most `pip` commands will still work if we change `pip` → `uv pip`.

## Why create Python scripts and packages?

Creating scripts and projects helps us keep our work organized
while making it significantly easier to reproduce our work
and to fix mistakes.

Let's initialize a project with `uv`:

```bash
uv init crane
```

Let's enter the `crane/` directory with:

```bash
cd crane
```

This command creates several files:

- `main.py` ← main Python project file
- `pyproject.toml` ← main configuration file
- `.python-version` ← which is currently `3.13`
- `README.md` ← a [Markdown] file that we can use for documentation
- `.gitignore` ← tells `git` to ignore certain files (might not be created)
- `.git/` ← directory managed by `git` (might not be created) 

Let's look at `pyproject.toml`,
the main _configuration file_ of a Python project.
The `cat` command (short for "concatenate") prints out the contents of files. 🐈

```bash
cat pyproject.toml
```

The file contains:

```toml
[project]
name = "crane"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.13"
dependencies = []
```

If we'd like, we could modify `description` to describe the purpose of this project.

```toml
description = "A sample project to learn uv"
```

> [!NOTE]
> [**TOML**] is a file format standard intended for use in configuration files.
> TOML files include
>
> - **Key-value pairs**, like `name = "crane"`,
> - **Tables**, like `[project]`, which are collections of key-value pairs,
> - **Arrays**, like `dependencies = []`, and
> - **Comments**, which start with a `#`.

> [!TIP]
> The Python Packaging User Guide describes
> [how to write a `pyproject.toml` file](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/).

Let's look at the contents of `main.py`:

```python
def main():
    print("Hello from crane!")


if __name__ == "__main__":
    main()
```

Executing `main.py` as a script prints out a line saying hello.

```bash
python main.py
```

> [!NOTE]
> The `if __name__ == "__main__"` block contains
> code that will be run when the file is _run as a script_.
> This block will not be run when the file is _imported_ in Python.

> [!TIP]
> Creating a `main()` function means that we can run it from
> _within Python_, not just when the file is executed as a script.

If we have already installed Python, we can execute the script with:

```bash
python main.py
```

> [!CAUTION]
> If our project had depended on Python packages (e.g., `numpy` or `astropy`),
> then we would have had to manually install them prior to running `python`.

> [!TIP]
> If we use `uv run` instead, then `uv` will take care of
> package installation for us!

```bash
uv run main.py
```

The directory now contains a file and a directory:

- `uv.lock` ← automatically generated file containing exact Python & package versions
- `.venv/` ← directory containing the virtual environment for this project

The command `uv run main.py` was run
within the virtual environment contained in `.venv/`,
using exact package versions defined in `uv.lock`.

### Adding and removing dependencies

Now suppose that we want to use Astropy to do some unit conversions.
Let's do the following commands:

```bash
uv add astropy
```

This command updates `pyproject.toml` to contain the following lines:

```toml
dependencies = [
    "astropy>=7.0.1",
]
```

Using `uv add` also updated the environments contained in `.venv/` and `uv.lock`.

> [!CAUTION]
> Sometimes new versions of packages have breaking changes.
> A function might have been removed, or moved to another location.

> [!TIP]
> Specifying the exact versions of dependencies makes it
> more likely that a script we create now
> will continue to work in the future,
> or if we share it with someone else.

To use an _exact_ version of astropy, run:

```bash
uv add astropy==7.0.1
```

We can also add and remove dependencies:

```bash
uv add numpy
uv remove numpy
```

Now let's modify `main()` in `main.py` to print
the number of teaspoons in 1 barn · megaparsec:

```python
from astropy import units


def main():
    volume = 1 * units.barn * units.Mpc
    print(volume.to(units.imperial.tsp))
```

and then run it with

```bash
uv run main.py
```

We can expand `main.py` for a research project,
such as doing data analysis for an experiment.

> [!TIP]
> For cleaner code, write _short functions_
> that _do one thing_
> with _no side effects_! ✅️

## Creating a Python package with `uv` (if time)

Next let's create a Python **package**, analogous to NumPy.
Let's navigate up a directory
so that we're not in a pre-existing project directory.

```bash
cd ..
```

Let's use `uv init` with the `--library` option to create `cranepy`:

```bash
uv init cranepy --library
```

And then see what's in the new `cranepy/` directory:

```bash
cd cranepy
ls
```

There are still `README.md`, `.python-version`, and `pyproject.toml` files.
Instead of `main.py`, there is now an `src/` directory.

Instead of `main.py`, the code is now in `src/cranepy`.

```bash
ls -A src/cranepy
```

This directory contains a `__init__.py` file, which is the file that
needs to be present for Python to treat a directory like a Python package.

```bash
cat src/cranepy/__init__.py
```

> [!IMPORTANT]
> An `__init__.py` file is necessary for a directory to be imported by Python.
> `__init__.py` files often import `.py` files from the directory and 

`__init__.py` files typically contain initialization code.
An `__init__.py` file is necessary in order for a directory to be imported by Python.

If we previously activated the virtual environment, then
we can install our new `cranepy` package with the command:

```bash
uv pip install -e .
```

The `-e` makes the installation "editable".
In Unix, `.` refers to the current directory.

If we run `python`, we can now import it!

```pycon
>>> import cranepy
>>> cranepy.hello()
```

[**toml**]: https://toml.io/en
[markdown]: https://www.markdownguide.org
[`bash`]: https://en.wikipedia.org/wiki/Bash_(Unix_shell)
[`uv`]: https://astral.sh/uv
