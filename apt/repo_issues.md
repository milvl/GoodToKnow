# APT / Repository Issues (Linux Mint / Ubuntu)

>`$` marks the beginning of a command line instruction.

## Overview:

Common problems with APT repositories can break updates, upgrades, or package installs. These issues typically come from third-party repositories, outdated configs, or missing keys.

---

## Missing GPG Keys (NO_PUBKEY):

APT requires signed repositories. If a key is missing, updates fail.

- **Error example:** `NO_PUBKEY XXXXX`
- **Cause:** Repository added without importing its signing key

### Fix (modern, supported):
  - `$ sudo mkdir -p /etc/apt/keyrings`
  - `$ curl -fsSL <repo_key_url> | sudo gpg --dearmor -o /etc/apt/keyrings/<name>.gpg`
  - Update repo to use:
    - `signed-by=/etc/apt/keyrings/<name>.gpg`

### Avoid:
  - `apt-key` (deprecated and unsafe)

---

## Deprecated / Broken Repositories:

Repositories may:
- Be removed
- Change URLs
- Stop being signed

### Symptoms:
- `repository is not signed`
- `Failed to fetch`
- 404 errors

### Fix:
  - `$ ls /etc/apt/sources.list.d/`
  - `$ sudo rm /etc/apt/sources.list.d/<broken_repo>.list`
  - `$ sudo apt update`

---

## Unsupported Distribution (Release Mismatch):

A repo may not support your OS version (e.g., using *jammy* repo on *noble*).

### Symptoms:
- `does not support noble`
- Packages not found

### Fix:
  - Remove or disable repo:
    - `$ sudo rm /etc/apt/sources.list.d/<repo>.list`
  - Re-add later using correct version (after upgrade)

---

## DEB822 (.sources) Format Issues:

Newer `.sources` files may not be supported by all tools.

### Symptoms:
- Upgrade tools refusing to proceed
- Errors mentioning “DEB822 format”

### Fix:
  - Temporarily remove:
    - `$ sudo mv /etc/apt/sources.list.d/<file>.sources /root/backup/`

---

## Third-Party / PPA Conflicts:

PPAs and external repos can override system packages.

### Symptoms:
- “Foreign packages”
- Version conflicts
- Upgrade blocked

### Fix:
  - Downgrade to official packages:
    - `$ sudo apt install <package>=<official_version>`
  - Or let tools handle it (recommended during upgrades)

---

## Orphan Packages:

Packages installed from removed repos remain but are unmanaged.

### Symptoms:
- Listed as “orphan packages”
- Not found in any repo

### Fix (optional):
  - `$ sudo apt autoremove`
  - Or remove specific packages:
    - `$ sudo apt remove <package>`

---

## Repository Still Exists After Uninstall:

Removing a package does NOT remove its repository.

### Symptoms:
- Errors from repos even after uninstalling software

### Fix:
  - `$ sudo rm /etc/apt/sources.list.d/<repo>*`
  - `$ sudo apt update`

---

## Architecture Warnings (i386):

Some repos don’t support 32-bit.

### Symptoms:
- `doesn't support architecture 'i386'`

### Fix (optional):
  - Ignore (harmless), or:
  - `$ sudo dpkg --remove-architecture i386`

---

## Best Practices:

- Always ensure:
  - `$ sudo apt update` runs **without errors**
- Prefer official repositories when possible
- Re-add third-party repos **after major upgrades**
- Use `/etc/apt/keyrings/` instead of deprecated methods
- Periodically clean:
  - `$ sudo apt autoremove`
- Avoid mixing distributions (e.g., jammy + noble)

---

## Quick Health Check:

  - `$ sudo apt update`
  - `$ apt policy`
  - `$ ls /etc/apt/sources.list.d/`

✔️ No errors = healthy system  
❌ Any warnings/errors = fix before proceeding
