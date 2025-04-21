# Creating Python scripts and packages with `uv`

In this session, 
we will learn how to create Python scripts and packages with [`uv`],
which is an extremely fast Python package and project manager. 

## Prerequisites

### Terminal access

### Installing `uv`

Please follow these [instructions to install `uv`](https://docs.astral.sh/uv/getting-started/installation/)

To make sure that `uv` is installed, open a terminal and run
```bash
uv version
```

## Outline

 - Creating and managing Python environments
 - 
 - Important files 
   - `pyproject.toml`
   - 


## Creating and managing Python environments


## Why create Python scripts and packages?



##

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
which is a [TOML] file that contains the metadata for the project. 
Let's use the command
```bash
cat pyproject.toml
```
which shows the contents of the file (`cat` is short for "concatenate"):
```toml
[project]
name = "my-project"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.13"
dependencies = []
```
We can edit `pyproject.toml` to add our own description.
Because  

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

> [!IMPORTANT]
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

> [!HINT]
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

Now let's modify `main.py` to print out 
the number of teaspoons in 1 barn · megaparsec:

```python
# In main.py

import astropy.units as u


def main():
    volume = 1 * u.barn * u.Mpc
    print(volume.to(u.imperial.tsp))
```

and then run it with

```bash
uv run main.py
```





[Markdown]: https://www.markdownguide.org
[TOML]: https://toml.io/en