# Developing Python Packages (DataCamp)

## From Loose code to Local Packages
Get your package started by converting scripts you have already written. You'll create a simple package which you can use on your own computer.

### Scripts, modules, packages, and Libraries
- **Script** - A Python file which is run like `python myscript.py`. It's designed to conduct one set of tasks.
- **Package** - A directory full of Python files which you import functions from (e.g. `numpy`). All the code in this directory are related and works together.
- **Subpackages** - A smaller package inside a package (e.g. `numpy.random` and `numpy.linalg`)
- **Module** - A Python file inside a package which stores the package code
- **Library** - Either a package or a collection of packages (e.g. Python's standard library which contains lots of Python packages such as `math`, `os`, `datetime`, etc)

### Directory tree of a package

Here is tree structure of a simple package directory:

```bash
../code/mypackage/
├── __init__.py
├── mysubdir
│   ├── __init__.py
│   ├── mysubsubdir
│   │   ├── __init__.py
│   │   └── simplemodule3.py
│   └── simplemodule2.py
└── simplemodule1.py

2 directories, 6 files
```

- `mypackage` is a Python package.
- `simplemodule.py` is a module, which contain all the package code.
- `__init__.py` marks this directory as a Pyhton package. It's an empty file.
- `mysubdir` is a subdirectory inside `mypackage`. It's marked as subpackage with `__init__.py` and so on.

Using subpackages helps organize our code. We should place closely related functions and classes in the same module and related modules in the same subpackage.

### Documentation
A really important part of developing packages is writing documentation.
- Good documentation means users can actually use your code.
- You should write documentation for each **function**, each **class**, and each **class method**.
- Users can access your documentation from within python using the `help()` function.
- There are different documentation styles (e.g. Numpy , Google). Numpy style is the most verbose style and is good for documentation of complex functions.

#### Function documentation
- The first sentence of documentation is a summary, and should read like a command; like you are telling the function what to do.

```Python
def count_words(filepath, word_li):
  """Count number of words in a text"""
```
- For simple functions,this is all you need. For complex functions, you need to add adidtional information:
    - Input parameters
        - Type
        - List mutiple types for parameter of appropriate (e.g. `axis : {int, tuple of int, None}`)
        - List accepted values if only a few valid options (e.g. `interpolation : {'linear', 'lower', 'higher'}`)
    - Returned values and types in order
    - Other sections:
        - `Raises`
        - `See Also`
        - `Notes`
        - `References`
        - `Examples`
        - Fore more infor, see [here](https://numpydoc.readthedocs.io/en/latest/format.html).

```Python
def count_words(filepath, word_li, search_type='normal'):
  """Count number of words in a text

  Parameters
  ----------
  filepath : str
      File path to text object that can be read into a str.
  word_li : list
      List of input words to search against text.
  search_type : {'normal', 'greedy'}
      Type of algorithm when searching a word against text.
  
  Returns
  -------
  n : int
      Number of words in the text.
  """
```

#### Documentation templates and style translation

You can use a number of tools to generate docstring templates. Certain AI tools can generate docstrings from an existing code too:
- `pyment` can be used to generate docstrings
- Run from terminal
- It can genearte many documentation styles from
    - Google
    - Numpydoc
    - reST (reStructured-text)
    - Javadoc (epytext)
- Modify documentation from one style to another

Here is how you can run `pyment` on a Python file.

```bash
pyment -w -o numpydoc my_file.py # Generates Numpy style template
pyment -w -o google my_file.py # Translates Numpy style to Google style
```

- `-w`: overwrites the file.
- `-o numpydoc`: output in Numpy style

#### Package, subpackage, and module documentation

This helps your users navigate your package and understand what it can do.

- Package and subpackage documentations are placed in a string at the top of their respective `__init__.py` files. This should summarize the package and subpackage, respectively.
- Module documentation is written at the top of the module file.

### Structuring imports

When you `import` the package, you cannot access any of the subpackages or modules. The package doesn't know about them. The subpackages don't know about the modules they contain either.

#### Importing subpackages
To resolve this, in the package's `__init__.py` file, we import the subpackages. We can do this using **absolute** or **relative** imports.
- Absolute: `from mypackage import subpackage` (preferred method)
- Relative: `from . import subpackage`

#### Importing modules
Similarly, inside `__init__.py` in the subpackage, we can import the modules:
- Absolute: `from mypackage.subpackage import module`
- Relative: `from . import module`

#### Importing between sibling modules in the same subpackage
If a module file is getting too big, you might split it into multiple files. Hiwever, we still need to use them inside the main module, so we need to import them. As usual, we can use absolute or relative imports to import the two functions. Therefore, inside main `module.py`, we add the following lines:

- Absolute: `import package.subpackage.sibkingmodule import (some_funcs)`
- Relative: `import .siblingmodule import some_funcs`

#### Importing between modules far apart
There might be classes or functions you want to use in many of your modules which live in different subpackages. In this case, inside the working modules you can import others using ablolute or relative path:
- Absolute: `from package.othermodule import some_funcs`
- Relative: `from ..othermodule import some_funcs`

**`utils` module:** It is usually used for small, often unrelated, pieces of code each of which which aren't enough to justify their own module.


## Install Your Packages From Anywhere
Make your package installable for yourself and others. In this chapter, you'll learn to deal with dependencies, write READMEs, and include licenses. You'll also complete all the steps to publish your package on PyPI—the main home of Python packages.

### Why should you install your own package?
- If you move the script and the package apart, you will no longer be able to import your package.
- This is because the script can search for packages in its **parent directory**, but it won't search outside that.
- If you install it, you can import the package no matter where the script is located.

### setup.py file
To make the package installable, you are going to add a new file to the package - the `setup.py` script.
- Is used to install the package
- Contains the metadata on the package
- Needed if you want to publish your package

#### New package directory structure
`setup.py` file is a part of the package, but not part of the source code. Therefore, we need create an outer dictectory with the same name of the package and place the `setup.py` inside it.

#### Inside setup.py

```Python
# Import required functions
from setuptools import setup, find_packages

# Call setup function
setup(
    author="Author's name",
    description="A short description of the package",
    name="A name for package",
    packages=find_packages(include=["mypackage", "mypackage.*"]) # used to find package and subpackages
    version="X.X.X",   
)
```

When versioning a custom package, the difference between `major`, `minor`, and `patch` releases is defined by the type of changes made to the code. This system, known as **Semantic Versioning (SemVer)**, uses a three-part number format: `MAJOR.MINOR.PATCH`.
1. **Patch Releases (PATCH)**
- **What it is:** This is for *backwards-compatible bug fixes.*
- **When to increment:** You increase the patch number when you fix an issue without changing any existing functionality or the public API.
- **Example:** If your version is `1.0.1`, and you fix a bug, the new version becomes `1.0.2`.

2. **Minor Releases (MINOR)**
- **What it is:** This is for *new, backwards-compatible functionality.*
- **When to increment:** You increase the minor number when you add new features or functionality in a way that doesn't break existing code. The patch number is reset to 0.
- **Example:** If your version is `1.2.5`, and you add a new feature, the new version becomes `1.3.0`.

3. **Major Releases (MAJOR)**
- **What it is:** This is for *backwards-incompatible changes.*
- **When to increment:** You increase the major number when you make changes that will break existing code that uses your package. This usually happens when you remove or fundamentally change public APIs. Both the minor and patch numbers are reset to 0.
- **Example:** If your version is `2.5.3`, and you make a breaking change, the new version becomes `3.0.0`.

These rules help users of your package understand the impact of an upgrade. A patch update is low-risk, a minor update is medium-risk, and a major update is high-risk and requires careful migration.

#### Editable installation
Once you have written the setup script you can install the package. You should navigate to the directory containing the setup file, and then you can use `pip` (standard package manager for Python) to install the package like this:

```bash
pip install -e .
```
- `.` =  install the package in the currect directory
- `-e` = install it in editable mode

*Editable mode* means when you make changes to the source code, like fixing a bug or adding new features, these changes are included when you import the package. If you didn't do this, you would need to reinstall the package each time you make a change.

For more information on `pip`, see [here](https://realpython.com/what-is-pip/#:~:text=Watch%20it%20together%20with%20the,of%20the%20Python%20standard%20library.).

### Dealing with dependencies

#### What are dependencies?
- Other packages you import inside your package
- Inside `mymodule.py` (e.g. `numpy`, `pandas`, etc)

#### Adding dependencies to setup.py

```Python
from setuptools import setup, find_packages

setup(
    ...
    install_requires=['numpy', 'pandas', 'matplotlib'],
)
```

In above file, pip would install any version of the required dependencies. Sometimes our code requires a specific version of another package. We can specify it on `install_requires` option as below:

```Python
from setuptools import setup, find_packages

setup(
    ...
    install_requires=[
        'numpy>=1.0',
        'pandas>=2.0.1,<3',
        'matplotlib==1.1'
    ],
)
```

Note that you should try to allow as many versions of these dependent packages as possible. If you restrict the version numbers too much then your user will not be able to install your package at the same time as other packages they need. You should also not have any unnecessary dependencies.

We can also specify which versions of Python they need to have:

```Python
from setuptools import setup, find_packages

setup(
    ...
    python_requires='>=2.7, !=3.0.*, !=3.1.*'
)
```

#### Choosing dependencies and package versions
- Check the package history or release notes
- Test different versions

#### Making an environment for developers
- A important part of developing any software is reproducibilty.
- Co-developere need to have the exact same versions of all the dependent packages so you can track down bugs.
- This is different to the `install_requires` dependencies where you tried to add as many different versions as possible. The `install_requires` is for **users** and the `requirements.txt` is for **developers**.
- Here, you want yto know exactly which versions you are using.
- You can show all the package versions you have installed using the `pip freeze` command.
- You should export this information into `requirements.txt` file which you include with your package. Then anyone can install all of the packages in this file later using this `pip install` command:

```bash
pip freeze > requirements.txt
pip install -r requirements.txt
```

### Including licences and writing READMEs

#### Why do I need a license?
- To give others permission to use your code
- There are many different licenses that you might include
- For open source licenses, find more information [here](https://choosealicense.com).
- All open source packages allow users:
    - use your package
    - modify your package
    - distribute versions of your package
- Add `LICENSE` file in package's parent direcotry, where other files such as `setup.py`, `requiremens.txt` live. 

#### What is a README?
- The "front page" of your package
- Displayed on Github or PyPI (Python package Index)
- README sections:
    - Title
    - Description and Features
    - Installation
    - Usage examples
    - Contributing
    - License

#### README formats

- Markdown
    -  Contained in `README.md` file
    -  Simpler
    -  Used more commonly
- reStructuredText
    - Contained in `README.rst` file
    - More complex
    - Also commonly used

You need to add `README.md` file into your package's parent directory.

### MANIFEST.in file

- `MANIFEST.in` is one last file you will need to create before you can release your package to the world.
- This file is used to list all of the extra files that you want to distribute along with your package.
- It is important because by default your license will not be included when someone downloads your package, and neither will your README. 
- Inside the `MANIFEST.in` file we just need to include the `LICENSE` and the `README` like this:

```text
include LICENSE
include README.md
```

### Publishing your package

#### PyPI
- Short for **Python Package Index**
- `pip` install packages from here
- Anyone can upload packages to it. You just need to register for a free account
- You should upload your package as soon as it might be useful

#### Distributions
- **Distribution package** - a bundled version of your package which is ready to install.
- **Source distribution** - a distribution package which is mostly your source code. Files need to be downloaded and installed using setup file.
- **Wheel distribution** - a distribution package which has been processed to make it faster to install. It's smaller in size and faster to download. It can be installed without running setup file. The wheel distribution is the preferred Python distribution, and pip will install a package using this when it is available.
- When you upload distributions to PyPI, it is good practice to upload both the wheel and the source distribution.

#### How to build distributions?

You can build source and wheel distributions from the terminal using this command:

```bash
python setup.py sdist bdist_wheel
```
- `sdist` = source distribution
- `bdist_wheel` = Wheel distribution

This will create a `dist` directory and add wheel and source distributions inside. It will also create build and `egg-info` directories, but you can ignore these.

#### Getting your package out there
- Upload your distribution to `PyPI` manually or using `twine` tool from command line:

```bash
twine upload dist/*
```

- You can also upload your distributions to the `Test-PyPI` repository, which is version of the PyPI respository made for **testing**.

```bash
twine upload -r testpypi dist/*
```

#### How other people can install your package

- Install package from PyPI:

```bash
pip install mypackage
```

- Install package from TestPyPI:
- 
```bash
pip install --index-url       https://test.pypi.org/simple
            --extra-index-url https://pypi.org/simple
            mypackage
```
You specify the `--index-url` which is where the package is downloaded from, and the `--extra-index-url` which is where `pip` can search for your dependency packages. 


## Increasing Your Package Quality
Bring your package up to a professional standard. Discover how to use pytest to guard against errors, tox to test if your package functions with multiple versions of Python, and flake8 to maintain great code style.

### Testing your package

- Good packages brag about how many tests they have
- 91% of the pandas package code has test
- Each function in your package should have a test function. For example: 

```Python
# A function in your package
def get_ends(x):
    reuturn x[0], x[-1]
```

```Python
# A test function for `get_ends()`
def test_get_ends():
    assert get_ends([1, 5, 39, 0]) == (1, 0)
    assert get_ends(list('payman')) == ('p', 'n')
```
- You should keep these `tests` in their own directory which is in the top folder of the package.
- The best way to lay out the tests directory is to copy the structure of the code directory.
- Every module in the code directory should have its own test module in the test directory (e.g. `test_module1.py`, `test_module2.py`, etc).
- Inside the test module, there should be a test function for each function defined in the source module. Remember that you will need to import the functions you are testing from the main package. This should be done using an **absolute import**.

#### Runnig tests with pytest
To run tests, navigate to parent package directory where `tests/` direcotry lives and run the following command on command line:

```bash
pytest tests/
```

- `pytest` looks inside the test directory.
- It looks for modules like `test_modulename.py`.
- Inside test modules, it looks for test funnctions like `test_function_name()`.
- It runs these functions and shows output

#### Testing multiple versions of Python

If you are trying to write a package which will work with **multiple versions of Python**, then you will need to test each version separately.

One way to do this is following the below steps:
- Install all these Python versions
- Install your package and all dependencies into each python 
- Run `pytest`

There is an easier and the most common way to do the above steps and it is using a tool called `tox`. 
- `tox` is a tool specifically built to run package tests with multiple versions of Python.
- You can run it from command line just like `pytest`
- In order to use `tox`, you need to create a configuration file, `tox.ini`, telling tox what to run in each environment and also which Python versions to use.
- `tox.ini` needs to be placed in top level of the packahe, alongside `setup.py` file.

Inside `tox.ini` file, we need to put the following information:
- Headings are surrounded by brackets (e.g. [tox])
- To test Python version X.Y add `pyXY` to `envlist`
- The versions of Python you test need to be installed already. `tox` won't install them on its own.
- Under the `[testenv]` heading you need to tell `tox` what you want it to do once it has installed your package.
- The `deps` parameter tells tox what are its dependencies. For example, you need to specify `pytest` as a tox dependency.
- You use the `commands` parameter to tell tox to run `pytest` and other shell commands that you run on terminal.
- Once you create a functional `tox.ini` file, then you can run `tox` on a terminal inside the top level of the package direcotry.

Here is an example of a `tox.init` file:

    [tox]
    envlist = py27, py35, py36, py37

    [testenv]
    deps = pytest
    commands = 
        pytest
        echo "run more commands"

### Keeping your package stylish

#### Introducing flake8

- Standard Python style is described in `PEP8`'
    - It covers how variables and funnctions shold be named
    - How your code should be laid out
- `pytest` is used to find bugs
- `flack8` is used to find styling mistakes
    - It's a static code checker - reads code but doesn't run it
    - You can run it on terminal: `flake8 script.py`
    - Note that modern IDEs have built-in static code checker, which is much easier to use. (so I won't focus too much on `flake8`.)


## Rapid Package Development
Create your packages more quickly. In this final chapter, you’ll learn how to use cookiecutter to generate all the supporting files your package needs, Makefiles to simplify releasing new versions, and be introduced to the last few files your package needs to attract users and contributors.

### Faster package development with templates
- A Python package has a lot of additional files you need to write.
- There is a lot to remember and fill in.
- `cookiecutter` is a command line tool which creates projects from scratch.
    - It creates a basic, empty Python package with all additional files your package need.
    - You run `cookiecutter` from terminal and pass it a URL of the template you'd like to use

      ```bash
      cookiecutter <template-url>
      
      ```

    - Standard template for Python packages is `https://github.com/audreyr/cookiecutter-pypackage.git`
    - Once you run it, it prompts you for more details used to fill in the template, including package name and slug.
    - The slug is just the name you want the package to be registered under. The package will be pip-installed with this name.
    - You don't need to worry too much here becuase you can always manually modifiy these files later.

#### Version numbers and history

**CONTRIBUTING.md:**
- Either markdown or reStructured-Text
- Invite other developers to work on your package
- Tells them how to get started
- cookiecutter template is a great start but you can modify it as you wish.

**HISTORY.md:**
- Known as history, changelog or release notes
- Tells users what has changed between versions
- Content of the file:
    - Section for each released version
    - Bullet points of the important changes
    -Subsections for:
        - Improvements to existing functions
        - New additions
        - Bugs that have been fixed
        - Deprecations

Here is how a `HISTORY.md` file would look like:

<img src='../images/history_file.png' width=500>

**Version number:**
- Increase version number when ready for new release.
- Cannot upload to PyPI if not changed.
- There are two places you need to update version number:
    - Inside top-level `__init__.py` file for user. It allows users to print package version: `print(mypackage.__version__)`
      ```python
      __version__ = '0.1.0'
      ```
    - Inside `setup.py` file
- We can incease version number using `bumpversion` in the top-level of the package.

  ```bash
  bumpversion major # to increase major number
  bumpversion minor # to increase minor number
  bumpversion patch # to increase patch number
  ```

#### Makefiles and classifiers

**Classifiers:**
- Metadata for your package
- Help users find your package on PyPI
- Added to `classifiers` argument in the `setup.py` file. You should include:
    - Package status
    - Your intended audience
    - License type
    - Language
    - Verisons of Python supported
- Lots more classifiers exist [https://pypi.org/classifiers](https://pypi.org/classifiers)
  
Here is how classifiers are included in the `setup.py` file:

<img src='../images/classifiers.png' width=500>

**Makefiles:**
- Speed up your developement
- Contains all terminal commands that we manually enter one by one
- You can use by running it with `make`: `make <function-name>`
    - To build source and wheel package: `make dist`
    - View `Makefile` summary: `make help`