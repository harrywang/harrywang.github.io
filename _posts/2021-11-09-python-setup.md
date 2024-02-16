---
layout: post
title: How to Setup Mac for Python Development
tags: tutorial
permalink: python-setup
---

This is how I setup Python development on my Mac.

### Homebrew

Install [Homebrew](https://brew.sh/)

```
http://brew.sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### Python

It's much easier to manage different versions of python using pyenv (https://github.com/pyenv/pyenv), which you can install using:

```
brew update
brew install pyenv
```

Check the different python versions you can install:

```
pyenv install --list
```

Install a specific python version (choose from the list above):

```
pyenv install 3.12.0
```

Then, check the python versions installed:

```
pyenv versions
  system
  3.7.10
  3.8.10
  3.8.11
  3.9.7
  3.10.0
* 3.10.6 (set by /Users/harrywang/.pyenv/version)
  3.12.0
```

Set the global python version:

```
pyenv global 3.10.6
```

You may also need to do the following to make sure the pyenv python will be executed instead of the Mac default one:

```
eval "$(pyenv init --path)"
```

To make the above command work after reboot, run `vim ~/.zshrc` and add `eval "$(pyenv init --path)"` to the file.


if you want to use a specific python version within a specific folder, go to that folder and set a local python version:

```
pyenv local 3.9.7
```

PS: setup `pyenv` on Ubuntu:

```
ssh hjwang@128.175.21.xxx  # ssh to the remote server
sudo apt update -y  # update apt
sudo apt install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev python-openssl git  # install dependencies
git clone https://github.com/pyenv/pyenv.git ~/.pyenv  # clone the repo
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc  # setup environment
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc  # setup environment
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n eval "$(pyenv init -)"\nfi' >> ~/.bashrc  # setup environment
exec "$SHELL"  # restart the shell
pyenv install --list  # available versions to install 
pyenv versions  # installed versions
pyenv install 3.9.1  # install a Python version
pyenv global 3.9.1  # set global python version to use
```


### Github

follow https://help.github.com/articles/set-up-git/ to install Github CLI and cache your credentials (choose HTTPS during the process):

```
brew install gh
gh auth login
```

Once completed, try the following two commands to verify:

```
$ git config --global user.name
Harry Wang
$ git config --global user.email
harryjwang@gmail.com
```

Caching your GitHub password in Git: https://help.github.com/articles/caching-your-github-password-in-git/

### Virtual Environment

See why you need virtual environments: https://realpython.com/python-virtual-environments-a-primer/

To create a virtual environment:
- go to the folder of the project
- create a `requirements.txt` to include the packages  
- create a `venv` folder for the virtual environment, activate it and install packages: 

```
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### VSCode

Just download the installation package and install from [https://code.visualstudio.com/](https://code.visualstudio.com/)


### CHANGELOG

This post was originally shared on [Medium](https://harrywang.medium.com/how-to-setup-mac-for-python-development-37e5fd895151) on August 30, 2016.
