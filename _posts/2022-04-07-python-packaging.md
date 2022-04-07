---
layout: post
title: "How to Create Python Packages"
author: Vijay Senapathi, Aditya Jaiswal
twitter_image: "https://blog.qure.ai/assets/images/python/structure.png"
categories:
tags:
    - Python
    - AI
---

Have you faced the problem of writing same lines of code for every project? That's where python packaging come to the rescue. A Python package is nothing but a folder containing some modules inside it. Python packages have made our life easy as we just have to import the functionality we need from the required package. Just imagine if someone had not built those packages, how tedious life of programmers had been. 

This blog aims to cover the basics and what you need to publish a python package successfully.

### Structure of the Project

<center>
<img width='100%' src="/assets/images/python/structure.png" alt="project structure"/>
</center>

To initialize a folder as a Python package we need to create a file called `__init__.py` inside the folder.`__init__.py` will run everytime we try to import the package in our project.

### Setup.py

setup.py is crucial which contains the metadata of our package and binds everything our module have. It allows us to do `python setup.py install` and `pip install`.

```python
# Always prefer setuptools over distutils
from setuptools import setup, find_packages
# To use a consistent encoding
from codecs import open
from os import path

here = path.abspath(path.dirname(__file__))

# Get the long description from the README file
with open(path.join(here, 'README.md'), encoding='utf-8') as f:
    long_description = f.read()
version = open('VERSION').read().strip()
requirements = open('requirements.txt').read().split('\n')

setup(
    name='package_name',
    version='#version',
    description='what does the package do',
    long_description=long_description,
    author = 'author name',
    author_email = 'author email',
    packages=package,
    install_requires=requirements,
    python_requires=">=3.6"
)
```
`name` - This is the name our package<br>
`version` - The version number to be visible on PyPi website<br>
`description` - The description of the package to be displayed on the PyPi website<br>
`long description` - detailed description of the package and usage<br>
`author` - name of the author to be displayed on PyPi website<br>
`author_email` -author's email to be displayed on PyPi website<br>
`packages` - The packages to be built and uploaded to the PyPi website<br>
`install_requires` - Package dependency<br>
`python_requires` - Minimum python version requirement<br>


There are multiple types of python package builds:

* [Wheels](https://packaging.python.org/discussions/wheel-vs-egg/) - Binary builds for each platform or universal. Highly recommended.
* [Eggs](https://packaging.python.org/discussions/wheel-vs-egg/) - Deprecated. Don’t use.
* Source tars - When wheel for target platform is unavailable, setup.py from source code is run again during installing


### Upload to PyPi

PyPi is where we can store all the built packages/source tars. We need to first upload the package to PyPi website before we can do pip install. 

To start the upload, first sign up on the PyPi website and install the `twine` python package to upload any package. 
```python
python3 -m pip install --upgrade twine
```

Next, we build our package for distribution:
```python
python3 -m build
```
After the build command, we will see dist folder in the root directory of the root directory of our project. These distribution files are created by python which can be installed on any system. 

The final step is to upload the distribution files to the website. 
```python
python3 -m twine upload --repository testpypi dist/
```
This will prompt for username and password after which the package will be uploaded to the website.

### Testing

* Regression tests: Write these tests whenever you fix a bug - so that we never ever encounter them **again.**
* Unit tests: Write these tests for every new feature built.


Super simple instructions to write tests for python package:

```bash
mkdir tests
cd tests
ln -s ../your_package_name .
touch test.py   # write your tests here
py.test
```
You have now successfully created your own custom python package and uploaded it to PyPi website. You can now simply do pip install packagename and use it in any project required. 
