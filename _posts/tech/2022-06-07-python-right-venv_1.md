---
title: "Don't Let Python Stop You (1/2)"
subtitle: "Create the Right Environment"
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

## The right tool for the right job

Python is one of the most popular programming languages in the world. It has a large user base, and is often used in web
development, scientific computing, and data analysis. However, Python does not come pre-installed on all systems, and it
can be difficult to set up the right environment for using it. This article will describe how to set up a decent Python
environment on MacOS.

## Setup Python Pyenv Environment

In this article, we are going to learn how to set up a Python Pyenv environment. Pyenv is a tool that allows you to
manage multiple Python versions on your system. It is an alternative to virtualenv and pipenv.

Pyenv requires a few components in order to work properly.

```
brew install pyenv
```

In my case I needed to install Xcode for the CommandLineTools

```
xcode-select --install
sudo xcode-select --switch /Library/Developer/CommandLineTools # this is optional if you encounter further issues
```

You can insert this into your "~/.zprofile". If you work with bash, you just insert it into your "~/.bash_profile". If
it does not exist, you can just create one. It should work out of the box. Here you can see my .zprofile:

```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
if command -v pyenv 1>/dev/null 2>&1; then
	eval "$(pyenv init -)"
fi
```

Now you can list all possible python version that can be installed

```
pyenv install -l
```

In contrast, with the versions command you can check which versions are locally already available.

```
pyenv versions
```

In our case, we don't have a Python version available from pyenv.

That's why we need to choose a version and install it. In my case I chose Python version 3.10.4.

```
pyenv install 3.10.4
```

After you have downloaded it you can test the comfort of Pyenv. You can set a global and a local python version.

Check out your global version first and ensure that it is not the same as we just downloaded. Now you can change your
directory to a test folder and activate your local python version with

```
pyenv local 3.10.4
```

Start your python console and check your version. Now you can see your last downloaded python version in your console.

```
Python 3.10.4 (main, Jun  7 2022, 11:24:12) [Clang 13.0.0 (clang-1300.0.27.3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
```


With this validated cross check, you can now set your global python version easily.

```
pyenv global <version>
```

With this prerequisite we are now ready to change our python versions on the fly. 
In my next blog post you can read more about the installation of a
dependency resolver and virtual environment manager called poetry.

### resources

Many thanks to

- [vinzee, 2020, Stackoverflow](https://stackoverflow.com/questions/52522565/git-is-not-working-after-macos-update-xcrun-error-invalid-active-developer-pa)
- [Agustin Castro, 2022, betterprogramming.pub](https://betterprogramming.pub/5-reasons-why-poetry-beats-pip-python-setup-6f6bd3488a04)
- [poetry documentation](https://python-poetry.org/docs/)

