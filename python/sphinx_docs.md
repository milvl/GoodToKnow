# Sphinx Docs

1.  `$ mkdir myproject`
2.  `$ cd myproject`
3.  `$ python -m venv venv_docs`
4.  `$ source venv_docs/bin/activate` (On Windows, use: `venv_docs\Scripts\activate`)
5.  `$ pip install sphinx`
6.  `$ sphinx-quickstart`
7.  `docs/conf.py`:
    ```python
    import os
    import sys
    sys.path.insert(0, os.path.abspath('..'))
    extensions = [ ... 'sphinx.ext.autodoc', ... ]
    ```
8.  `$ cd path/to/your/docs/`
9.  `$ sphinx-apidoc -o source/ ../path_to_your_python_modules`
10. `index.rst`:
    ```rst
    .. toctree::
    :maxdepth: 2
    :caption: Contents:
    modules
    ```
11. `$ make html`
