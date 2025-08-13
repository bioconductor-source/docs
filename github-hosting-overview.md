# GitHub Hosting Transition - Overview

For hands‑on instructions (remotes, redirects, authentication) see the [Developer Implementation Guide](./github-hosting.md).

## Executive Summary
Bioconductor is migrating authoritative package repositories from a self‑hosted gitolite service (`git.bioconductor.org`) to GitHub under the `bioconductor-source` organization.

## Implementation Overview

### Current State
* Packages live in a gitolite-managed server accessed via SSH (`git@git.bioconductor.org:packages/<pkgname>.git`).
* Web access uses URLs like `https://git.bioconductor.org/packages/<pkgname>` (which historically exposed cgit or similar views).
* Authentication / authorization is handled by gitolite keys maintained by the core team.
* Package source code can be browsed at `https://code.bioconductor.org` separately.

### Target State
* Authoritative repositories: `https://github.com/bioconductor-source/<pkgname>`.
* The existing Apache server (the same host that currently fronts gitolite) continues to answer for `git.bioconductor.org`, redirecting to the appropriate GitHub location
* Apache issues **permanent 301 redirects** from legacy package paths to GitHub, preserving deep links, encouraging caches and clients to update remotes automatically over time.
* The server for `https://code.bioconductor.org` is deprecated and rolled into the above Apache redirect, to also encourage browsing directly in GitHub.

## Redirect Strategy
Permanent (301) redirects for `git.bioconductor.org/packages/<pkg>` paths to `github.com/bioconductor-source/<pkg>` so: 
* Search engines update indexing.
* Tooling that follows redirects (most modern git clients for HTTPS) keeps functioning until remotes are updated.
* A centralized switch reduces need for piecemeal communication of new URLs.

## Impact on Package Maintainers
Pros:
* Standard GitHub fork + Pull Request workflow increases visibility of external contributions.
* Easier collaboration with broader open-source community (contributors already have GitHub accounts, no more auth registration process).
* Optionally opens up space for public issues about packages,

Cons:
* Need to update local `origin` remotes (SSH: `git@github.com:bioconductor-source/<pkg>.git` or HTTPS) and possibly adjust authentication (PAT vs legacy gitolite key).
* Initial learning curve for those unfamiliar with PR review features / branch protection.

Mitigations:
* Clear guide providing "cheat sheet" for common tasks and links to detailed documentation


---
Next: For technical implementation details proceed to the [Developer Implementation Guide](./github-hosting.md).
