# Git CLI

## Init:

  - `$ git init`
  - `$ git clone [<ssh_host_from_config>]<repository_url> [<destination_folder>]`

## Check info:

  - `$ git status` -\> *show only local changes*
  - `$ git log` -\> *show all commits to branch*
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
