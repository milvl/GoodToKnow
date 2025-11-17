# Git LFS (Large File Storage)

## What It's For

- Keep big/binary files (e.g., `*.psd`, `*.mp4`, `*.zip`) out of normal Git history.
- Git stores tiny pointer files; real blobs live in an LFS store on the remote (GitHub/GitLab/Bitbucket must have LFS enabled).

### Install (once per machine)

- macOS (Homebrew): `$ brew install git-lfs`
- Windows (Chocolatey): `$ choco install git-lfs`
- Linux (Deb/RPM): `$ sudo apt-get install git-lfs` | `$ sudo dnf install git-lfs`
- Initialize hooks (once after install): `$ git lfs install`

### Enable in a Repository

- Track patterns BEFORE adding files:
    ```bash
    $ git lfs track "*.psd" "*.mp4" "assets/binaries/**"
    $ git add .gitattributes
    $ git add <large files>
    $ git commit -m "Track large binaries via LFS"
    $ git push origin <branch>
    ```

### Check Info

- `$ git lfs ls-files` -\> list files tracked by LFS
- `$ git lfs status` -\> LFS-specific status

### Example .gitattributes (auto-created by track)

```text
*.psd filter=lfs diff=lfs merge=lfs -text
*.mp4 filter=lfs diff=lfs merge=lfs -text
assets/binaries/** filter=lfs diff=lfs merge=lfs -text
```

## Clone & Pull

- Standard clone (fetches LFS blobs on checkout):
    - `$ git clone <repo>`
- Skip downloading blobs on clone (CI/low bandwidth):
    - `$ GIT_LFS_SKIP_SMUDGE=1 git clone <repo>`
    - Later fetch blobs: `$ git lfs pull --include="assets/binaries/**"` or `$ git lfs pull`
- Fetch LFS objects without checkout: `$ git lfs fetch`
- Recreate working files from pointers: `$ git lfs checkout`
- Clean up old local LFS blobs: `$ git lfs prune`

## Push

- Same as normal Git once tracked; just commit & push:
    ```bash
    $ git add <files>
    $ git commit -m "<message>"
    $ git push <origin | URL to repo> <branch name>
    ```

## Migrating Existing History to LFS (history rewrite; coordinate with team)

- Move already-committed big files into LFS:
    - `$ git lfs migrate import --include="*.mp4,*.zip"`
- Limit to specific ref:
    - `$ git lfs migrate import --include="*.mp4" --include-ref=refs/heads/main`
- After migration, force-push:
    - `$ git push --force-with-lease origin <branch>`

## File Locking (prevent concurrent edits on binaries)

- `$ git lfs lock path/to/file.bin`
- `$ git lfs unlock path/to/file.bin`
- List locks (optional): `$ git lfs locks`

## Stop Tracking a Pattern

- `$ git lfs untrack "*.mp4"`
- `$ git add .gitattributes`
- `$ git commit -m "Stop LFS tracking for *.mp4"`
    - *Note: Already-tracked files remain in LFS; new commits for that pattern won’t use LFS.*

## Notes & Good Practices

- Define `$ git lfs track` rules BEFORE the first commit of large files.
- Remotes may have LFS bandwidth/storage quotas; monitor usage.
- CI often needs: `$ git lfs install && git lfs fetch && git lfs checkout`
