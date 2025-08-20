# GitHub Hosting Developer Implementation Guide

This guide covers concrete mechanics of the transition from gitolite at `git.bioconductor.org` to GitHub (`bioconductor-source` org) and how to update local tooling.

### Redirect Pattern
```
Legacy: https://git.bioconductor.org/packages/<pkgname>
301 -> https://github.com/bioconductor-source/<pkgname>
```

## Main Differences for Maintainers & Contributors

### 1. SSH Access Changes
Legacy SSH remotes of the form:
```
origin	git@git.bioconductor.org:packages/<pkgname>.git
```
will no longer function once gitolite is retired.

You must instead configure your remote to use GitHub. The canonical SSH form is:
```
git@github.com:bioconductor-source/<pkgname>.git
```
(Or use the HTTPS form shown later.)

If you need to (re)configure:
```
# From inside your local clone
git remote set-url origin git@github.com:bioconductor-source/<pkgname>.git
```

You will then need to make sure the ssh key from your git.bioconductor.org account (or a new SSH key) is also accepted by GitHub.

#### Summary steps for using existing key:

For most, the SSH key accepted for your git.bioconductor.org connection should already be registered with your GitHub account, as that has been recommended for some time.
You can verify that you have a public key uploaded to GitHub by going to `https://github.com/<your-username>.keys`.
If the key that you use for git.bioconductor.org already has a corresponding public key uploaded to your GitHub profile, you only need to add the appropriate portion to `~/.ssh/config` for the GitHub SSH connection.

If you do not know the path at which your git.bioconductor.org private key file resides, you may look for the `IdentityFile` line output by this command to help you find it:
```bash
grep -A3 'git.bioconductor.org' ~/.ssh/config
```
Once identified, edit your `~/.ssh/config` file accordingly:

```bash
cat << "EOF" >> ~/.ssh/config
Host github.com
     IdentityFile ~/.ssh/id_rsa_bioconductor
EOF

#### Summary steps for adding new key:

Refer to the full GitHub SSH authentication guide for generating and registering keys:
https://docs.github.com/en/authentication/connecting-to-github-with-ssh

1) I have a private key at `~/.ssh/id_rsa` and a public key at `~/.ssh/id_rsa.pub`. I use `pbcopy < ~/.ssh/id_rsa.pub` to save the contents of the public key to clipboard.

2) I go to [https://github.com/settings/keys](https://github.com/settings/keys) on my GitHub profile, and upload my public key portion.

3) I add the appropriate portion to `~/.ssh/config`:
```bash
cat << "EOF" >> ~/.ssh/config
Host github.com
     IdentityFile ~/.ssh/id_rsa
EOF
```

4) I use `git push` as usual. Note: It is expected and normal for you to get a prompt confirming your first SSH connection.
```
The authenticity of host 'github.com (140.82.112.4)' can't be established.
XXXXXX key fingerprint is SHA256:+XXXXXXxxxxxXXXXXXXxxxxXXXXXX.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```

### 2. HTTPS Access & Authentication
Existing HTTPS clone / fetch patterns that begin with `https://git.bioconductor.org` will be redirected to the GitHub equivalent and should continue to work, but authentication is now handled by GitHub, not gitolite.

TL;DR for HTTPS auth:
* Use your GitHub username as the username.
* Use a Personal Access Token (PAT) (recommended) instead of a password.
* You can create either:
  * A classic PAT with broad (repo) scope for convenience; or
  * A fine-grained PAT restricted to specific repositories for tighter security.

Git will prompt for credentials on the first push; store them in the macOS keychain / credential helper as usual. Detailed PAT management documentation:
https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens

Example switching to HTTPS:
```
git remote set-url origin https://github.com/bioconductor-source/<pkgname>.git
```
When pushing you will be prompted (first time) for:
```
Username: <YourGitHubUsername>
Password: <Paste PAT>
```

### 3. Authorization Model
Previously, write access was granted by adding SSH keys to gitolite. Under GitHub hosting, repository write / admin access is controlled through standard GitHub collaborator & organization permissions.

Two main contribution patterns are supported:

1. Direct Collaborator (Write Access)
   * Similar to the legacy approach for long-term or core maintainers.
   * Request addition by emailing maintainer@bioconductor.org (include GitHub username and package name(s)).
   * Core team adds the user as an external collaborator (or org member with appropriate team) granting push rights.

2. Fork & Pull Request (Recommended for temporary / drive-by contributions)
   * Contributor forks `bioconductor-source/<pkgname>` to their own GitHub account.
   * Implements changes in a feature branch and opens a Pull Request (PR) against the upstream repository.
   * Maintainers review, request changes, and merge when ready.

Benefits of the fork/PR model: clearer review workflow, CI integration on PRs, easier traceability of contributors, and no need to grant broad write access preemptively.

## FAQ (Seed)
**Will my existing local clones break?**
Fetches may still work for a limited time via HTTPS (redirect) but SSH to gitolite will fail—update your `origin` remote proactively.
