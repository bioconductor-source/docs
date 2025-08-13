# Bioconductor Infrastructure Transition Docs

This repository is the working documentation hub for Bioconductor's transition from the self‑hosted and self-maintained infrastructure (e.g. gitolite + legacy BBS build system) to a new model that:

* Hosts package source repositories natively on GitHub under the `bioconductor-source` organization.
* Leverages the rOpenSci/r-universe build and publication ecosystem for automated, reproducible builds.
* Distributes package artifacts (binaries, tarballs) from bucket storage rather than VMs

## Table of Contents

* GitHub Hosting
	* [Strategic Overview](./github-hosting-overview.md) – rationale, goals, benefits, risks
	* [Developer Implementation Guide](./github-hosting.md) – redirects, auth changes, contribution workflows

---

Feel free to open issues or pull requests against this repository to propose clarifications, corrections, or additional topics that would help maintainers and contributors navigate the transition.
