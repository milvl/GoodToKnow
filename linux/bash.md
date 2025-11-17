# Linux

## Posix operators:

| Cmd 1 | Operator | Cmd 2 | Result |
| :--- | :---: | :--- | :--- |
| first | `;` | second | first then second |
| first | `&` | second | first (in background) and second launched |
| first | `&&` | second | If first is successful (return code 0) then second |
| first | `||` | second | If first is unsuccessful (return code not 0) try second |
| first | `\|` | second | first then give output to second |
| first | `\|&` | second | Passes both standard output and standard error of one command as input to another |
| | `!` | | Negation |

## Redirections:

| Cmd | Operand | Stream | Result |
| :--- | :---: | :--- | :--- |
| cmd | `<` | file.txt | file.txt passed to cmd |
| cmd | `<>` | file.txt | Same as above, but the file is open in read+write mode instead of read-only |
| cmd | `>` | out.txt | Output of cmd saved as ./out.txt (special: `cmd 1> out.txt 2> errOut.txt`) |
| cmd | `>\|` | out.txt | Does the same as `>`, but will overwrite the target even if the shell is configured to refuse overwriting (`set -C` or `set -o noclobber`) |
| cmd | `>>` | out.txt | Appends output of cmd to out.txt (otherwise creates it) |
| ## Special##  | | | `>&` : (per POSIX spec) when surrounded by digits (`1>&2`) or `-` on the right side (`1>&-`) either redirects only one file descriptor or closes it (`>&-`) |

## Variable in this shell:

- `export var=<value>` -\> create value (don’t use export in script)
- `$var` -\> access value

## Search for a file/folder:

- `tree –f / | grep –e ".*your_substring.*"` -\> may take a while
- `find <name>`

## Check if script is sourced (bash script):

```bash
if [[ "${BASH_SOURCE[0]}" == "${0}" ]]; then
    echo "The script was executed directly, it needs to be launched with 'source/.'"
    echo "Usage: source build.sh"
    echo "Usage: . build.sh"
    exit –1
else
    echo "Script is sourced."
fi
```