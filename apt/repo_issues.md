# APT / Repository Issues (Linux Mint / Ubuntu)

> `$` marks the beginning of a command line instruction.

## Overview:

Common APT problems come from third-party repositories, release mismatches, missing signing keys, stale package metadata, or leftover packages from removed repositories. On modern Ubuntu-based systems, repository keys should be scoped with `Signed-By` and stored in `/etc/apt/keyrings/`, and phased updates can legitimately defer some upgrades. ([Debian Manpages][1])

---

## Safe First Response:

Before changing anything major:

* `$ sudo apt update`
* `$ apt list --upgradable`
* `$ grep -RInE '^(deb|URIs:|Suites:|Components:|Signed-By:)' /etc/apt/sources.list /etc/apt/sources.list.d/ 2>/dev/null`

If `apt update` shows errors, fix those first before running `upgrade` or `full-upgrade`.

---

## Missing GPG Keys / Signature Problems:

APT requires signed repositories. If the repository key is missing or the repo is signed with an unknown key, updates fail. Modern guidance is to avoid `apt-key` and use per-repository keyrings with `Signed-By`. ([Debian Manpages][1])

### Symptoms:

* `NO_PUBKEY XXXXX`
* `The following signatures couldn't be verified`
* `repository is not signed`

### Fix (modern, supported):

* `$ sudo mkdir -p /etc/apt/keyrings`
* `$ curl -fsSL <repo_key_url> | sudo gpg --dearmor -o /etc/apt/keyrings/<name>.gpg`
* In the repo definition, use:

  * `signed-by=/etc/apt/keyrings/<name>.gpg` in a `.list` entry
  * or `Signed-By: /etc/apt/keyrings/<name>.gpg` in a `.sources` entry

### Avoid:

* `apt-key` (deprecated)
* Importing third-party keys into the global trusted keyring unless absolutely necessary

---

## Deprecated / Broken Repositories:

Repositories can break because they were removed, changed URL, stopped publishing packages for your release, or lost valid signing metadata.

### Symptoms:

* `Failed to fetch`
* `404 Not Found`
* `repository is not signed`
* `Release file is missing`

### Fix:

* `$ ls /etc/apt/sources.list.d/`
* `$ grep -RInE '^(deb|URIs:)' /etc/apt/sources.list /etc/apt/sources.list.d/ 2>/dev/null`
* Disable or remove the broken entry:

  * `$ sudo rm /etc/apt/sources.list.d/<broken_repo>.list`
  * `$ sudo rm /etc/apt/sources.list.d/<broken_repo>.sources`
* `$ sudo apt update`

### Safer alternative to deletion:

* Rename the file first:

  * `$ sudo mv /etc/apt/sources.list.d/<broken_repo>.list /root/<broken_repo>.list.disabled`
  * `$ sudo mv /etc/apt/sources.list.d/<broken_repo>.sources /root/<broken_repo>.sources.disabled`

---

## Unsupported Distribution / Release Mismatch:

A repo may target the wrong Ubuntu base release, such as using `jammy` on a `noble` system. Mixing releases is a common cause of blocked upgrades and dependency conflicts.

### Symptoms:

* Packages kept back unexpectedly
* Packages not found
* Dependency conflicts
* A third-party repo line contains the wrong suite name (`jammy`, `focal`, `noble`, etc.)

### Check your system release:

* `$ . /etc/os-release && echo "$UBUNTU_CODENAME ${VERSION_CODENAME:-}"`

### Fix:

* Inspect repo files:

  * `$ grep -RInE 'jammy|focal|noble|wilma|victoria|virginia' /etc/apt/sources.list /etc/apt/sources.list.d/ 2>/dev/null`
* Disable or remove the mismatched repo:

  * `$ sudo rm /etc/apt/sources.list.d/<repo>.list`
  * `$ sudo rm /etc/apt/sources.list.d/<repo>.sources`
* Re-add it later only if the vendor supports your current release
* `$ sudo apt update`

### Rule:

* Do not mix Ubuntu releases in APT sources unless you deliberately know how to pin and manage them

---

## `.sources` / DEB822 Files:

Modern APT supports DEB822-style `.sources` files, and they are valid on current Ubuntu-based systems. The usual problem is not the format itself, but a malformed entry, wrong suite, wrong key path, or an older third-party tool that expects `.list` files. ([Ubuntu Manpages][2])

### Symptoms:

* `apt update` errors referring to a `.sources` file
* Bad `Signed-By` path
* Wrong `Suites:` value
* Third-party tooling refusing a source definition

### Fix:

* Inspect the file:

  * `$ sudo nano /etc/apt/sources.list.d/<file>.sources`
* Verify:

  * `URIs:`
  * `Suites:`
  * `Components:`
  * `Signed-By:`
* If needed, temporarily disable it:

  * `$ sudo mv /etc/apt/sources.list.d/<file>.sources /root/<file>.sources.disabled`
* `$ sudo apt update`

### Do not assume:

* `.sources` is broken just because it is not a `.list` file

---

## Third-Party Repositories / PPAs Causing Conflicts:

PPAs and vendor repos can override or block official packages. This is especially common after major distro upgrades.

### Symptoms:

* Packages kept back
* Version conflicts
* Unexpected candidate versions
* Upgrade blocked until packages are removed or changed

### Check a package’s source:

* `$ apt-cache policy <package>`

### Fix:

* Disable the third-party repo temporarily
* `$ sudo apt update`
* Check the candidate again:

  * `$ apt-cache policy <package>`
* Then upgrade normally:

  * `$ sudo apt upgrade`
  * or, if needed and after reviewing changes:
  * `$ sudo apt full-upgrade`

### Notes:

* `apt upgrade` is conservative
* `apt full-upgrade` may install or remove packages to satisfy dependencies
* Only use `full-upgrade` after `apt update` is clean and you understand the proposed changes

---

## Kept-Back Packages:

A package can be kept back either because new dependencies/removals are required, or because the update is being phased. Ubuntu documents phased updates as normal behavior. ([Ubuntu Documentation][3])

### Symptoms:

* `The following packages have been kept back`
* `upgrades have been deferred due to phasing`

### Check safely:

* `$ apt list --upgradable`
* `$ sudo apt upgrade --simulate`
* `$ sudo apt full-upgrade --simulate`

### Fix:

* If the package is only deferred due to phasing, waiting is usually safest
* If the package is kept back due to dependency changes:

  * `$ sudo apt full-upgrade`
* Review the proposed removals before accepting

---

## Obsolete / Leftover Packages After Repo Removal:

Packages installed from removed repositories can remain installed even though no currently configured repository provides them anymore. Debian documents this as a real post-upgrade cleanup scenario. ([Debian][4])

### Symptoms:

* Package is installed but cannot be re-downloaded
* `apt-cache policy <package>` shows only:

  * `/var/lib/dpkg/status`
* `aptitude search '~o'` lists the package as obsolete

### Checks:

* `$ apt-cache policy <package>`
* `$ aptitude search '~o'`

### Fix options:

* Remove it if no longer needed:

  * `$ sudo apt remove <package>`
* Or replace it with the current repo-backed package if the name changed
* Check reverse dependencies first:

  * `$ apt-cache rdepends --installed <package>`

### Important:

* “Obsolete” does not always mean “unsafe to keep”
* It means APT no longer has a currently configured source for updates or reinstall

---

## Packages Automatically Installed but No Longer Required:

These are dependency leftovers, not necessarily repository issues.

### Symptoms:

* `The following packages were automatically installed and are no longer required`

### Fix:

* `$ sudo apt autoremove`

### Safer check first:

* `$ sudo apt autoremove --simulate`

---

## Repository Still Exists After Uninstalling Software:

Removing an application package does not remove its repository, keyring, or source file.

### Symptoms:

* `apt update` still contacts the vendor repo
* Errors continue after uninstalling the software

### Fix:

* Find related files:

  * `$ ls /etc/apt/sources.list.d/`
  * `$ ls /etc/apt/keyrings/`
* Remove the repo file:

  * `$ sudo rm /etc/apt/sources.list.d/<repo>*`
* Remove the matching keyring only if you are sure it is no longer used:

  * `$ sudo rm /etc/apt/keyrings/<name>.gpg`
* `$ sudo apt update`

---

## Architecture Warnings (`i386`):

Some repositories do not publish 32-bit packages. This often causes warnings but not always a fatal problem.

### Symptoms:

* `Skipping acquire ... doesn't support architecture 'i386'`

### Fix options:

* Ignore it if you intentionally use `i386` support for Steam, Wine, or legacy software
* Otherwise remove the foreign architecture:

  * `$ dpkg --print-foreign-architectures`
  * `$ sudo dpkg --remove-architecture i386`
  * `$ sudo apt update`

### Warning:

* Do not remove `i386` if you still rely on 32-bit packages

---

## Best Practices:

* Always make sure:

  * `$ sudo apt update`
    runs without errors before upgrading
* Prefer official repositories when possible
* Re-add third-party repositories only after confirming they support your current release
* Use `/etc/apt/keyrings/` with `Signed-By` instead of `apt-key`
* Avoid mixing releases such as `jammy` and `noble`
* Use:

  * `$ apt-cache policy <package>`
    to see where a package comes from
* Review:

  * `$ sudo apt full-upgrade --simulate`
    before a major dependency-changing upgrade
* Periodically clean:

  * `$ sudo apt autoremove`

---

## Quick Health Check:

* `$ sudo apt update`
* `$ apt list --upgradable`
* `$ apt-cache policy`
* `$ ls /etc/apt/sources.list.d/`
* `$ grep -RInE '^(deb|URIs:|Suites:|Components:|Signed-By:)' /etc/apt/sources.list /etc/apt/sources.list.d/ 2>/dev/null`

### Package-specific check:

* `$ apt-cache policy <package>`

### Obsolete packages check:

* `$ aptitude search '~o'`

✔ No errors in `apt update`, correct release names, and sane `apt-cache policy` output = healthy system
❌ Signature errors, 404s, wrong release names, or packages sourced only from `/var/lib/dpkg/status` = investigate before proceeding

[1]: https://manpages.debian.org/testing/apt/apt-key.8.en.html "apt-key(8) — apt — Debian testing — Debian Manpages"
[2]: https://manpages.ubuntu.com/manpages/xenial/man5/sources.list.5.html "Ubuntu Manpage: sources.list - List of configured APT data ..."
[3]: https://documentation.ubuntu.com/project/how-ubuntu-is-made/concepts/phased-updates/ "Phased updates - Ubuntu project documentation"
[4]: https://www.debian.org/releases/stable/release-notes/upgrading.html "4. Upgrades from Debian 12 (bookworm) — release-notes ..."
