# Git CLI

>`$` marks the beginning of a command line instruction.

## Init:

  - `$ git init`
  - `$ git clone [<ssh_host_from_config>]<repository_url> [<destination_folder>]`

## Check info:

  - `$ git status` -\> *show only local changes*
    - `$ git status -u` -\> *show untracked files as well (otherwise might be collapsed into directory)*
  - `$ git log` -\> *show all commits to branch*
    - `$ git log -p -- path/to/file.ext` -\> *show incremental changes to a specific file*
  - `$ git branch` -\> *list all branches*

## Update:

  - `$ git fetch origin | <URL to repository>`

## Push:

  - `$ git add <files> | . | --all | -u`
    \-\> *--all is also with deletions, -u is only modified and deleted*
  - `$ git commit –m <message>`
  - `$ git push <origin | URL to repo> <branch name>`

## Jump to version:

  - `$ git checkout <hash from git log | branch name>`

## Branches:

  - `$ git branch <branch name>` -\> *create a branch*
  - `$ git branch –d <branch name>` -\> *delete a branch*
  - `$ git merge <name of the branch that will be merged to now selected>`

## Set specific SSH for a repository:

  - `$ git remote set-url origin git@github.com:user/repo.git -i ~/.ssh/another_key`

## Creating Tags:

  - `$ git tag <tagname>` -\> *lightweight tag, after the commit*
  - `$ git tag -a <tagname> -m "your message"` -\> *annotated tag*

### Tag a commit after the fact:

`$ git tag -a <tagname> <commitSHA> -m "your message"`

### List all tags:

  - `$ git tag`
  - `$ git tag –n` -\> *list tags with messages*
  - `$ git tag -l "v1.*"` -\> *list tags with a certain pattern*

### Pushing Tags

  - `$ git push origin <tagname>`
  - `$ git push origin –tags` -\> *push all tags to remote*

### Deleting Tags

  - `$ git tag -d <tagname>` -\> *delete a tag locally*
  - `$ git push --delete origin <tagname>` -\> *delete a tag from remote*

### Checking Out Tags

  - `$ git checkout <tagname>`

### Sharing Tags

After creating an annotated tag, you need to explicitly push the tag to the shared repository:
Push an annotated tag to remote:

  - `$ git push origin <tagname>`

### Fetching Tags

Fetching tags from a remote repository, if they are not automatically fetched:

  - `$ git fetch --tags`

### Viewing Tag History

  - `$ git log --tags`

### Tag Naming Conventions (Summary)

  * **Format:** Use [Semantic Versioning](https://semver.org/): `MAJOR.MINOR.PATCH`
      * Example: `v1.2.3`
      * Prefix with `v` (common convention)
  * **Stable releases:** `v1.0.0`, `v2.3.1`
  * **Pre-releases:**
      * Alpha (early, unstable): `v1.0.0-alpha`, `v1.0.0-alpha.1`
      * Beta (feature complete, unstable): `v1.0.0-beta`, `v1.0.0-beta.2`
      * Release candidate (potential final): `v1.0.0-rc.1`, `v1.0.0-rc.2`
  * **Other conventions:**
      * Nightly/Dev: `v1.0.0-nightly.20250908`, `v2.1.0-dev`
      * Hotfix: `v1.0.1-hotfix`
      * Long-term support: `v1.2.0-lts`
  * **Best practices:**
      * Use annotated tags: `git tag -a v1.0.0 -m "Release version 1.0.0"`
      * Push tags explicitly: `git push origin v1.0.0`
      * Tags should be immutable — never reuse for different code
      * Be consistent with your naming
      * Avoid moving tags like `latest` or `stable`

### Git Stash

  * `$ git stash` -\> stash current changes
  * `$ git stash push -m "custom message"` -\> stash with a message
  * `$ git stash list` -\> list all stashes
  * `$ git stash apply stash@{n}` -\> apply a specific stash
  * `$ git stash drop stash@{n}` -\> delete a specific stash
  * `$ git stash clear` -\> clear all stashes

### Git Conflicts

| Command | Type | Usage |
| :--- | :--- | :--- |
| `$ git pull origin main` | Fast-forward merge | Use when local is just behind remote, no conflicts. |
| `$ git pull origin main --no-rebase` | Regular merge | Use when you want to preserve both histories, even if messy. |
| `$ git pull --rebase origin main` | Rebase | Use in teamwork to keep history clean and linear, safer than force. |
| `$ git push --force-with-lease origin main` | Force push | Use only when you amended/rebased already-pushed commits and you’re sure no one else is depending on them. |
