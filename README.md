# QBiC Pipelines

This repository contains the documentation necessary to run different types of analysis in our clusters at QBIC.

## Contributing to the documentation

## Checking the documentation locally

To check locally how the documentation is building, you need to install the `sphinx`, `recommonmark`, `sphinx-rtd-theme` and `httpserver` dependencies. It requires Python 3!

```bash
pip install sphinx recommonmark httpserver sphinx-rtd-theme
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

Go to the address shown in your browser, navigate to the `_build` directory and you can check out locally the documentation.

## Deploying the documentation on intranet

To deploy the documentation in `qbic-intranet`, you need to install the `sphinx`, `recommonmark`, `sphinx-rtd-theme` and `httpserver` dependencies. It requires Python 3!

```bash
pip install sphinx recommonmark httpserver sphinx-rtd-theme
```

Then clone this repository from GitHub:

```bash
git clone git@github.com:qbic-pipelines/pipeline-docs.git
```

Go to the `/docs` directory of this repository and run:

```bash
make html
```

Then zip the `html` folder and transfer it to the `firefly` machine `qbic-intranet.am10.uni-tuebingen.de`. You will need to ask Matze the first time to add you to the users list and the sudoers list.

Then place the `html.zip` under `/var/www/html/landing_page/qbic_pipelines_docs/` and unzip it. You will need to remove the existing `html` directory first.

## Set up the server
