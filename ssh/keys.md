# SSH – keys

## Create new:

- `ssh-keygen -t ed25519 -C "your_email@example.com"`
- If not default:
    - `ssh-add <path to '../.ssh'>/.ssh/<name of the key>`

## To launch ssh agent if not running:

- `eval "$(ssh-agent -s)"` -> *linux*

## Using configs:

- Generate a new SSH key pair (if you haven't already):
    - `$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
- Add your SSH key to the SSH agent:
    - `$ eval $(ssh-agent)` <- if "Could not open a connection to your authentication agent."
    - `$ ssh-add ~/.ssh/your_key_rsa`
- Create or edit your SSH config file:
    - `$ vim/nano ~/.ssh/config`
- Add the SSH host configurations to your SSH config file:
- Example:

```ssh-config
# Personal GitHub account
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/github_key_rsa

# GitHub for University account
Host github-university
  HostName github.com
  User git
  IdentityFile ~/.ssh/github_uni_key_rsa
```

  - Save and exit the text editor.
  - Clone a Git repository using your SSH key:
  - For your personal account:
      - `$ git clone git@github.com:username/repo.git`
  - For your university account:
      - `$ git clone git@github-university:username/repo.git`
