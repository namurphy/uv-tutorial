# Creating Python scripts and packages with `uv`

In this tutorial,
we will learn how to create Python scripts and packages with [`uv`]:
a new Python package installer and manager written in Rust.
Even though `uv` was first released in February 2024,
it has seen rapid adoption because of the focus on performance, usability, and community.

## Installing `uv`

To follow along with this interactive live-coding tutorial,
please follow the [**installation instructions**] for `uv`.

> [!TIP]
> To test that `uv` is installed correctly, open a terminal or Powershell and type
>
> ```bash
> uv self version
> ```

## Why `uv`?

The Python packaging landscape is notoriously fragmented,
especially in contrast to newer languages like Rust.
[Astral] is working toward creating a unified toolchain for Python,
including `uv`, `ruff`, and most recently `ty`.

### Caching

`uv` uses an excellent [caching strategy]
to avoid re-downloading dependencies and registry information.

### Dependency resolution

[Dependency resolution] by `uv` is significantly faster than similar tools,
especially with a warm cache.

`uv` has capabilities that other tools lack, such as resolving environments for:
 - The lowest allowed versions of all or direct dependencies, and
 - Releases made up until a certain date.

### Universal lock files

`requirements.txt`-style files do not always work across different
versions of Python or different operating systems. 

`uv` can create a _single_ lock file that fully defines an environment
for multiple versions of Python, multiple operating systems, and all
dependency groups.

> [!NOTE]
> [PEP 751](https://peps.python.org/pep-0751/) recently defined the
> `pylock.toml` standard as a replacement for `requirements.txt` files.
> However, `pylock.toml` has limited capabilities compared to `uv.lock`.

> [!IMPORTANT]
> `pylock` would be a great name for a supervillain from a 1980s children's cartoon.

## Creating and managing Python environments

<!--
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
-->

A **virtual environment** is "an isolated space where you can work on
your Python projects, separately from your system-installed Python."
Let's create virtual environment using `uv`.

To keep things organized, let's create a temporary directory to work in.

```bash
mkdir uv_tutorial
cd uv_tutorial
```

Let's use `which` to see which `python` executable we are currently using.

```bash
which python
```

Let's [create a virtual environment](https://docs.astral.sh/uv/pip/environments)!

```bash
uv venv --python 3.13
```

Specifying the version of Python with `--python 3.13` isn't necessary,
but ensures that we're using the most recent version of Python.

Let's look at the full contents of the directory.

```bash
ls -A
```

> [!TIP]
> In PowerShell, use `dir` instead of `ls`.

The new directory called `.venv/` contains the virtual environment.
Let's check which `python` executable we're using:

```bash
which python
```

This is still the same `python` as before because
we still need to _activate_ the virtual environment.
The output of `uv venv` provides the command to activate the virtual environment,
which differs depending on which shell you're using

```bash
source .venv/bin/activate  # bash, sh, zsh
.venv/Scripts/activate  # PowerShell
source .venv/bin/activate.fish  # fish
source .venv/bin/activate.csh  # csh, tcsh
```

> [!NOTE]
> In PowerShell, it may be necessary to run the script as an administrator.

Now let's see which `python` we are using:

```bash
which python
```

Because we activated the virtual environment, the `python` we are using is
located at `.venv/bin/python` relative to the current directory.

> [!IMPORTANT]
> The virtual environment will need to be re-activated if we open a new terminal.

> [!TIP]
> To avoid having to manually activate our default environment,
> we can include the command in our shell configuration file
> (i.e., `.bashrc` for `bash`).

To install a package into the current virtual environment, use `uv pip`
as a drop-in replacement for `pip`:

```bash
uv pip install numpy
```

> [!TIP]
> Most `pip` commands will work if we change `pip` → `uv pip`.

We can deactivate a virtual environment with:

```bash
deactivate
```

## Creating projects

Let's initialize a project with `uv`.

```bash
uv init myproject
```

Let's enter the `myproject/` directory and see what is in it.

```bash
cd myproject
ls -A
```

This command creates several files:

- `main.py` ← main project file
- `pyproject.toml` ← configuration file
- `.python-version` ← currently `3.13`
- `README.md` ← a [Markdown] file that we can use for documentation
- `.gitignore` ← tells `git` to ignore certain files
- `.git/` ← directory managed by `git`

Let's look at `pyproject.toml`,
the main _configuration file_ of a Python project.

```bash
cat pyproject.toml
```

The file contains:

```toml
[project]
name = "myproject"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.13"
dependencies = []
```

<!--
> [!NOTE]
> [**TOML**] is a file format used in configuration files.
> TOML files include
>
> - **Key-value pairs**, like `name = "myproject"`,
> - **Tables**, like `[project]`, which are collections of key-value pairs,
> - **Arrays**, like `dependencies = []`, and
> - **Comments**, which start with a `#`.

> [!TIP]
> The Python Packaging User Guide describes
> [how to write a `pyproject.toml` file](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/).
-->

Let's see what's in `main.py`.

```bash
cat main.py
```

The contents of `main.py` are:

```python
def main():
    print("Hello from project!")


if __name__ == "__main__":
    main()
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

Then let's see what has changed.

```bash
ls -A
```

The directory now contains a file and a directory:

- `uv.lock` ← automatically generated file containing exact Python & package versions
- `.venv/` ← directory containing the virtual environment for this project

The command `uv run main.py` was run
within the virtual environment contained in `.venv/`,
using exact package versions defined in `uv.lock`.

### Adding and removing dependencies

Now suppose that we want to use Astropy in the project.

```bash
uv add astropy
cat pyproject.toml
```

The `uv add` command updated `pyproject.toml` to contain the following lines:

```toml
dependencies = [
    "astropy>=7.1.0",
]
```

Using `uv add` updated the environments contained in `.venv/` and `uv.lock`.

To use an exact version of astropy, run:

```bash
uv add astropy==7.0.1
```

We can also add and remove dependencies:

```bash
uv add sunpy
uv remove sunpy
```

<!--
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
-->

## Creating a Python package with `uv` (if time)

Next let's create a Python **package**.
Let's navigate up a directory
so that we're not in a pre-existing project directory.

```bash
cd ..
```

Let's use `uv init` with the `--library` option to create `package`:

```bash
uv init mypackage --library
```

And then see what's in the new `package/` directory:

```bash
cd mypackage
ls -A
```

Instead of `main.py`, there is now an `src/` directory.

```bash
ls -A src/mypackage
```

This directory contains `__init__.py` and `py.typed`.

```bash
cat src/package/__init__.py
```

> [!NOTE]
> An `__init__.py` file is necessary for a directory to be imported by Python.

> [!NOTE]
> `py.typed` indicates that a package uses type hint annotations.

Let's create a virtual environment for the project.

```bash
uv venv --python=3.13
```

Let's activate the environment.

```bash
source .venv/bin/activate
```

Now let's install our `mypackage` with the command:

```bash
uv pip install -e .
```

> [!NOTE]
> The `-e` makes the installation "editable".

If we run `python`, we can now import it!

```pycon
>>> import mypackage
>>> mypackage.hello()
```

> [!TIP]
> To learn more, check out the [`uv`] documentation.

[**toml**]: https://toml.io/en
[markdown]: https://www.markdownguide.org
[`bash`]: https://en.wikipedia.org/wiki/Bash_(Unix_shell)
[`uv`]: https://astral.sh/uv
[**installation instructions**]: https://docs.astral.sh/uv/getting-started/installation
[caching strategy]: (https://docs.astral.sh/uv/concepts/cache)
[dependency resolution]: https://docs.astral.sh/uv/concepts/resolution/
[Astral]: https://astral.sh