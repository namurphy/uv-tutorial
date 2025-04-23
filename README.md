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
uv venv 
```

Let's use `ls` to list the contents of the directory. 
We need to use the `-A` flag to show everything, 
since files and directories starting with a dot are hidden by default. 

```bash
ls -A
```

There is a new directory called `.venv/` which contains the virtual environment.

Let's check which `python` executable we're using:

```bash
which python
```
This is still the same `python`



The output of `uv venv` tells us the command to _activate_ a virtual environment.
For Unix, this is

```bash
source .venv/bin/activate
```

Now if we do

```bash
which python
```

we see that we are using the version of Python located in the `.venv` subdirectory.

To install a package into the virtual environment, use

```bash
uv pip install plasmapy
```

## Why create Python scripts and packages?

```bash
uv init my-project
cd my-project
ls
```

This command will create four files:

- `main.py` ← main Python project file
- `pyproject.toml` ← main configuration file
- `.python-version` ← which is currently `3.13`
- `README.md` ← [Markdown] file

Let's look at `pyproject.toml`,
which is the main _configuration file_ of a Python project.
Let's use the `cat` command (short for concatenate)
to see the contents of `pyproject.toml`:

```bash
cat pyproject.toml
```

to show the contents of the file:

```toml
[project]
name = "my-project"
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
> [TOML] is a file format standard intended for use in configuration files.
> TOML files include
>
> - _Key-value pairs_, like `name = "my-project"`,
> - _Tables_, like `[project]`, which can contain multiple key-value pairs,
> - Arrays, like `dependencies = []`, and
> - Comments, which start with a `#`.

> [!TIP]
> The Python Packaging User Guide describes
> [how to write a `pyproject.toml` file](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/).

Next let's look at the contents of `main.py`:

```python
def main():
    print("Hello from my-project!")


if __name__ == "__main__":
    main()
```

When we execute this file as a script, it will print out a line saying hello.

> [!NOTE]
> The `if __name__ == "__main__"` block contains
> code that will run when the file is run as a script.
> This block will not run when the file is imported in Python.

> [!TIP]
> Creating a `main()` function means that we can run it from
> _within Python_, not just when the file is executed as a script.

If we have already installed Python, we could run it with

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
> Specifying the exact versions of dependencies makes it significantly
> more likely that the script will continue to work in the future.

If we wanted to use an _exact_ version of astropy, we could do:

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
import astropy.units as u


def main():
    volume = 1 * u.barn * u.Mpc
    print(volume.to(u.imperial.tsp))
```

and then run it with

```bash
uv run main.py
```

> [!TIP]
> For cleaner code, write _short functions_
> that do _one_ thing
> with _no side effects_.

[markdown]: https://www.markdownguide.org
[toml]: https://toml.io/en
[`uv`]: https://astral.sh/uv
