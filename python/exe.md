# Make EXE (PyInstaller)

## In venv (OS dependent build)

  * `$ pip install pyinstaller`
  * `$ pyinstaller [args] myscript.py`

## Args

  * `--name=NAME`: Specifies the name of the output file (without the '.exe' or '.app' suffix).
  * `--onefile`: Generates a single executable file. Without this, PyInstaller creates a folder with an executable and several supporting files.
  * `--noconsole` or `–windowed`: Does not provide a console window for standard i/o. Useful for GUI applications.
  * `--add-data=<SRC;DEST or SRC:DEST>`: Includes additional non-Python files (like images or data files). The syntax differs slightly between Windows (`;`) and Unix-style systems (`:`).
  * `--add-binary=<SRC;DEST or SRC:DEST>`: Includes additional binary files.
  * `--icon=ICON_FILE`: Specifies an icon file for the executable.
  * `--hidden-import=MODULENAME`: Includes a module that is not automatically detected by PyInstaller.
  * `--paths=DIR`: Sets additional paths to search for imports.
  * `--runtime-hook=FILE`: Adds a custom runtime hook file.
  * `--exclude-module=MODULENAME`: Excludes a specific module.
  * `--version-file=FILE`: Adds a version resource from FILE to the executable.
  * `--upx-dir=DIR`: Specifies the path to the UPX utility, used for compressing executables.
  * `--clean`: Clears the PyInstaller cache and removes temporary files before building.
  * `--debug`: Provides more detailed output for diagnosing issues.
  * `--distpath=DIR`: Specifies the path to where the built distribution files should be placed.
  * `--workpath=DIR`: Sets the path for internal working files.
  * `--specpath=DIR`: Sets the folder to store the .spec file.
