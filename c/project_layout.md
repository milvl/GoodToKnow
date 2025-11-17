# Project Layout

```
my_project/
│
├── Makefile           # Compilation instructions for the project.
│
├── bin/                 # Compiled binaries.
│   ├── debug/           # Binaries for debugging.
│   └── release/         # Release binaries.
│
├── build/               # Intermediate build files, like object files.
│   ├── debug/           # Object files for debugging.
│   └── release/         # Object files for release.
│
├── doc/                 # Documentation for the project.
│   ├── api/             # API documentation (e.g., Doxygen generated).
│   └── manual/          # User manuals or other documentation.
│
├── include/             # Header files that expose the public interface of your libraries.
│   ├── mylib1/          # Each library can have its directory.
│   └── mylib2/
│
├── lib/                 # Compiled static or shared libraries.
│
├── src/                 # Source files.
│   ├── mylib1/          # Source files for each library.
│   │   ├── file1.c
│   │   └── file2.c
│   ├── mylib2/
│   │   ├── file1.c
│   │   └── file2.c
│   └── main.c           # Main application source.
│
├── test/                # Unit tests.
│   ├── mylib1/
│   │   ├── test1.c
│   │   └── test2.c
│   ├── mylib2/
│   │   ├── test1.c
│   │   └── test2.c
│   └── test_main.c      # Main test runner.
│
└── tools/               # Supplementary tools or scripts for the project.
```