# Documentation of the ROSACE project

Last updated: October 2026

The user documentation of ROSACE, built with Sphinx. It is published at
[https://rosace-astronomy.github.io/rosace-docs/](https://rosace-astronomy.github.io/rosace-docs/).

The source code of ROSACE itself is on Codeberg:
[https://codeberg.org/FrancoisCochard/Rosace-manager](https://codeberg.org/FrancoisCochard/Rosace-manager).

## Install the tools

Once, from this directory (the one holding this file):

```
python3 -m venv rosace-docs
source rosace-docs/bin/activate
pip install -r requirements.txt
```

Afterwards, only `source rosace-docs/bin/activate` is needed before building.

## Update the HTML documentation

From this directory:

```
make html
cp -r _build/html/* docs/
```

(the `mkh` script does both), then commit and push: GitHub Pages publishes
the `docs/` folder.

Note: the `.nojekyll` file in `docs/` is needed for GitHub Pages to show the
documentation.

## Update the PDF version

```
make simplepdf
```

The PDF is written to `_build/simplepdf/`.
