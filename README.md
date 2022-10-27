# QBiC Pipelines

[![Documentation Status](https://readthedocs.org/projects/pipeline-docs/badge/?version=latest)](https://pipeline-docs.readthedocs.io/en/latest/?badge=latest)

This repository contains the documentation necessary to run different types of analysis in our clusters at QBIC.

## Contributing to the documentation

To contribute to the documentation, please fork your repository and make a pull request. After review, your changes will be introduced to the documentation.

## Checking the documentation locally

To check locally how the documentation is building, you need to install the `sphinx`, `recommonmark`, `sphinx-rtd-theme` and `httpserver` dependencies. It requires Python 3!

```bash
conda create -n rtd python=3.9 pip
conda activate rtd
pip install sphinx recommonmark httpserver sphinx-rtd-theme sphinx_markdown_tables
```

Then clone this repository from GitHub:

```bash
git clone git@github.com:qbic-pipelines/pipeline-docs.git
```

Go to the `/docs` directory of this repository and run:

```bash
make html
```

All the html files will be build under the `_build` directory. Then you can view the documentation in your browser by using the python built-in server:

```bash
python -m http.server
```

Go to the address shown in your browser, navigate to the `_build` directory and you can check out locally the documentation by opening the file `_build/html/index.html` in a browser.

## Deploying the documentation to readthedocs

This documentation is now hosted on readthedocs at: [https://pipeline-docs.readthedocs.io](https://pipeline-docs.readthedocs.io).
The documentation deployment is automated by changes to the GitHub repository.
