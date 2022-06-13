---
title: "Don't Let Python Stop You (2/2)"
subtitle: "Create the Right Dependencies"
layout: post
author: "Malte"
header-style: text
tags:

- Pyenv
- Poetry
- Python
- Venv
- Dependency-Management

---

## Poetry for dependency management

As we've seen in the first article, it is crucial to switch to different python version and pyenv helped us with this.
If you haven't read this article, I highly recommend it.

Dependency management is a critical part of software development. It can be difficult to keep track of all the
dependencies your project relies on, and it's easy to introduce new ones unintentionally. A well-defined dependency
management strategy can help you avoid these problems.

There are a number of different dependency management tools available, each with its own strengths and weaknesses. The
most popular is definetely pip. So why using another depended resolver?

When choosing a dependency management tool, it's important to consider your team's needs and preferences. Some teams
prefer a tool that is widely used and supported, while others might prefer a tool that is more tailored to their
specific needs.

What I do like about poetry is, that the update and installation are encapsulated via a lock file and a toml file. You
can add a library to your toml file and poetry tries to resolve the dependencies with the lock file. If everything works
just fine you will not get any errors. But if you receive one, you can focus on that specific library with this version
and how to add or update it. This is one killer feature for me, why I chose poetry.

Additionally, I appreciate the easy process of building my new package.
And now we can see it in action.

## Poetry installation

This chapter is extremely short, because it is just one command and I don't want to bother you with unnecessary text.

```
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -

```

You can restart your shell or you just source the poetry directory and you are ready to go.

```
source $HOME/.poetry/env
```

That's it. Now you are ready to go.

If something has changed, please check the [official documentation](https://python-poetry.org/docs/).

## Poetry in action

First of all, I like to have my venv within my project.

```
poetry config virtualenvs.in-project true
```

Now we can start with how I create my project

First of all I create a new project folder with

```
poetry new foo
```

This is gonna be the directory tree of our new project.

```
.
├── README.rst
├── foo
│ └── __init__.py
├── pyproject.toml
└── tests
  ├── __init__.py
  └── test_foo.py

2 directories, 5 files
```

For now you don't see any lock file and this is because we did not add any dependency.

Let's just add one dependency which shouldn't be missed in any of my projects, numpy.

```
poetry add numpy 
```

```
Using version ^1.22.4 for numpy

Updating dependencies
Resolving dependencies... (1.0s)

Writing lock file

Package operations: 9 installs, 0 updates, 0 removals

 • Installing pyparsing (3.0.9)
 • Installing attrs (21.4.0)
 • Installing more-itertools (8.13.0)
 • Installing packaging (21.3)
 • Installing pluggy (0.13.1)
 • Installing py (1.11.0)
 • Installing wcwidth (0.2.5)
 • Installing numpy (1.22.4)
 • Installing pytest (5.4.3)
```

Et Voila, we have a poetry.lock file. Btw, with this command we just added numpy and all its dependencies (i.e. pytest).
We can also check this out in our lock file. In our pyproject.toml, we can only see numpy and it's version. There are
also many more possibilities with version constraints, but for now it should be fine.

Let's have a look at the pyproject.toml:

```
[tool.poetry]
name = "foo"
version = "0.1.0"
description = "This is just a poetry demonstration"
authors = ["Malte"]

[tool.poetry.dependencies]
python = "^3.8"
numpy = "^1.22.4"

[tool.poetry.dev-dependencies]
pytest = "^5.2"

[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"
```

We can basically differentiate the toml file in 4 blocks.
The [tool.poetry] is for metadata of the package.
[tool.poetry.dependencies] is for dependencies in a production system
[tool.poetry.dev-dependencies] is for dev-dependencies
[build-system] is used for the build system ( I know, I should not explain it with the same words, but I hope it's self
explanatory ;-) )

If you want to add a dev-dependency you can just do it like

```
poetry add -D pytest
```

and you are good to go.

Furthermore you can add another block to customize the dependency installation to your needs.

As an example you want a complete installation of dask. In this case you would achieve this with pip

```
pip install dask[complete]
```

In our case, we just want to add dask with poetry and afterwards make it optional.

```
poetry add "dask[complete]"
```

With the result

```
dask = {extras = ["complete"], version = "^2022.5.2"}
```

But now, we don't want this package all the time to be installed, only in some cases.
So we just add another block to our pyproject.toml and change the dask entry:

```
dask = { extras = ["complete"], version = "^2022.5.0", optional = true }

[tool.poetry.extras]
ml = ["dask"]
```

Now poetry listens to the shell parameter --extras ml. If you are now going to install the package with the command

```
poetry install --extras ml --no-dev
```

you install all packages that are not optional (by default), also all packages bundled in the parameter "ml" and you
don't install dev-dependencies. Fancy, isn't it?

Now you can just checkout your python venv with

```
poetry shell
```

and start coding in your src folder (here it is foo).

Whenever you feel like updating a dependency, you can just do

```
poetry update # update all packages
poetry update numpy # update single package
```

After all the coding fun, you just want to build and publish it.

```
poetry build #build wheel and tar.gz
poetry build --format wheel
```

With just one command you create your wheel and tar.gz file

```
.
├── README.rst
├── dist
│ ├── foo-0.1.0-py3-none-any.whl
│ └── foo-0.1.0.tar.gz
├── foo
│ └── __init__.py
├── poetry.lock
├── pyproject.toml
└── tests
  ├── __init__.py
  └── test_foo.py

3 directories, 8 files
```

You might realize that you just build your package with the incorrect version. Don't worry, you don't have to open
pyproject.toml by hand, poetry can help you out!

```
poetry version prerelease # 0.1.1-alpha.0
poetry version patch # 0.1.1
poetry version minor # 0.2.2
```

Now you are ready to publish it to your private repository, but before you have to add your repository path and
credentials:

```
poetry config repositories.foo https://foo.bar/simple/
poetry config http-basic.foo username password
```

With just one command you are able to publish your new package:

```
poetry publish
```

We have accomplished building our own package and publish it to our private repository.
In the future you/your customer want to consume your package. For this you need to
set up your pyproject.toml, so that you use your private repository as well. You remember, when we first installed numpy
and dask. By default, poetry looks for package at pypi. But as you may have learned, we can easily change that.

Just add this to your pyproject.toml

```
[[tool.poetry.source]]
name = "foo"
url = "https://foo.bar/simple/"
default = true
```

## Upgrade older projects to poetry

There are two more things, I'd like to add and had to do it quite often. You might have older projects, which you want
to upgrade to poetry. You can do that and it is not that complicated. (spoiler: We need some bash magic for automation)
.

First of all:
Go to your project root folder and create a pyproject.toml.

```
poetry init -n
```

I like to create my toml file in noninteractive mode. That's why I added this parameter.

Now you just need your requirements.txt and your pyproject.toml to be in the project folder and run this command.

```
RUN awk '{print $1}' requirements.txt | xargs -n1 poetry add
```

It just adds every library line by line and ignoring (!) the version.
If pip did a good job, we should have a working virtual environment, managed by poetry.

### Export to requirements.txt

You can't avoid to create a requirements.txt (at least in the near future). Poetry has of course a converter for this.

```
poetry export -o requirements.txt --extras ml --no-dev --without-hashes
```

I just added all parameters that came to my mind. You want of course the ml extras from above, and you also want to
ignore the dev- dependencies. The last parameter "without-hashes" is just to make it more readable and ignore the hashes
that are created from the poetry.lock file.

## Conclusion

Poetry is great tool and it helped me a lot during my projects. There are many feature that I really appreciate
like the dependency resolver or the build and publish process of poetry. Of course, poetry is not perfect, but they have a great and active community. 

I hope this article helped you and let me know if you missed something.

Many thanks to

- [vinzee, 2020, Stackoverflow](https://stackoverflow.com/questions/52522565/git-is-not-working-after-macos-update-xcrun-error-invalid-active-developer-pa)
- [Agustin Castro, 2022, betterprogramming.pub](https://betterprogramming.pub/5-reasons-why-poetry-beats-pip-python-setup-6f6bd3488a04)
- [poetry documentation](https://python-poetry.org/docs/)