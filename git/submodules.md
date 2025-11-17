# Git Submodules

> Short: **Submodules** let you embed one Git repository inside another as a dependency, pinned to a specific commit.  
> Use them when you need the other project's source in your repo but want to keep its history and lifecycle separate.

## Key concepts
- **Superproject**: your main repository that contains the submodule(s).
- **Submodule**: another Git repository included at a specific path inside the superproject.
- **Pointer**: the superproject records a *commit id* for each submodule (so the submodule is pinned).
- **`.gitmodules`**: file in the superproject that lists submodule URLs and paths.
- **Detached HEAD**: by default submodules are checked out at a commit, not a branch — that's why you'll often see a detached HEAD inside them.

## Common workflows & commands

### 1. Add a submodule
Use this to add a dependency repo:
```
git submodule add <repo-url> <path>
git commit -m "Add submodule: <path>"
```
This:
- Creates the folder at `<path>` and clones the remote into it.
- Adds a `.gitmodules` entry and stages the submodule pointer.

### 2. Clone a repository that contains submodules
Either clone then init/update:
```
git clone <super-repo-url>
git submodule init
git submodule update
```
Or clone with submodules in one go:
```
git clone --recurse-submodules <super-repo-url>
```
If you already cloned without `--recurse-submodules`:
```
git submodule update --init --recursive
```

### 3. Update submodules to the recorded commit
To ensure each submodule is checked out to the commit recorded by the superproject:
```
git submodule update
```
For nested submodules:
```
git submodule update --init --recursive
```

### 4. Move a submodule to a different commit (update pointer)
If you want your superproject to point to a newer commit of the submodule:
```
cd path/to/submodule
git fetch origin
git checkout <commit-or-branch-or-tag>
# (make changes / tests inside submodule if needed)
cd ../..                # go back to superproject
git add path/to/submodule
git commit -m "Update submodule <path> to <commit-or-tag>"
git push
```
Important: you must commit the changed pointer in the superproject — otherwise other users won't see the update.

### 5. Track a branch (optional)
Submodules normally track a commit. To pull the latest of a branch:
- First, in the superproject, set the branch in `.gitmodules` or use:
```
git config -f .gitmodules submodule.<path>.branch <branch-name>
git submodule sync
git submodule update --remote --merge <path>
```
Or globally:
```
git submodule update --remote --merge
```
This will fetch upstream for submodules and update the working copy to the tip of the configured branch — **but** you still need to `git add` and commit the new pointer in the superproject.

### 6. Pull changes to submodules
A quick way to pull in each submodule's current branch:
```
git submodule foreach 'git pull'
```
Or for recursive:
```
git submodule foreach --recursive 'git pull'
```

### 7. Remove a submodule
To remove a submodule cleanly:
```
git submodule deinit -f <path>
rm -rf .git/modules/<path>
git rm -f <path>
git commit -m "Remove submodule <path>"
```
This removes the entry from `.gitmodules`, removes the work tree, and removes the Git metadata.

## `.gitmodules` example
This file lives at the superproject root:
```
[submodule "libs/foo"]
    path = libs/foo
    url = https://git.example.com/foo.git
    branch = main
```
If you set `branch`, `git submodule update --remote` can use it to fetch newer commits.

## Practical tips & gotchas
- **Detached HEAD**: inside a submodule you'll usually be at a commit, not at a branch. If you intend to work inside the submodule, check out a branch (`git checkout -b my-work`) and push changes to that submodule repo.
- **Pushing**: pushing the superproject does **not** push commits made inside the submodule. You must push in the submodule repo, then commit the new pointer in the superproject and push the superproject.
- **CI**: ensure CI clones submodules (use `--recurse-submodules` or run `git submodule update --init --recursive`) and has credentials for private submodules.
- **Access for private submodules**: CI or other developers need access (SSH keys or tokens) to the submodule remote.
- **Conflicts**: submodule pointer conflicts are possible in merges. Resolve by choosing the correct submodule commit, then commit the resolution.
- **Shallow clones**: submodules + shallow clones can be tricky. If you need depth, pass `--depth` options carefully.
- **Moving/renaming path**: manually editing `.gitmodules` and running `git submodule sync` is required to keep remotes in sync.

## When to use submodules (vs alternatives)
- **Use submodule** when:
  - You depend on source code of another repo and want to keep it as a separate project.
  - You want reproducible builds tied to exact commits.
  - The dependency has its own lifecycle and CI.

- **Avoid submodule** when:
  - You want automatic live updates whenever the dependency changes (unless you accept manual `update --remote` workflows).
  - Your team dislikes the added git complexity.
  - You prefer a simpler strategy like vendorizing the code, using package managers, or `git subtree`.

## Troubleshooting quick wins
- Submodule shows missing: run `git submodule update --init --recursive`.
- You made changes in a submodule but commit doesn't show in superproject: `cd sub; git push; cd ..; git add sub; git commit -m "update sub pointer"; git push`.
- Resolve submodule merge conflicts by checking out the desired commit in the submodule and committing the updated pointer.

## Cheat sheet (most-used commands)
```
git submodule add <url> <path>
git clone --recurse-submodules <repo>
git submodule update --init --recursive
git submodule update --remote --merge
git submodule foreach --recursive 'git pull'
git submodule deinit -f <path>
git submodule sync --recursive
```
