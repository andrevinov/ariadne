# Ariadne Distribution Targets

This document defines Ariadne's intended distribution targets. It does not
choose build tooling or release automation.

## Docker

Ariadne must provide a Docker image for deployment and scheduled execution in
controlled environments.

The image must not contain user secrets, browser profiles, cookies, or local
runtime history.

## Python Package Artifacts

Ariadne must produce versioned Python package artifacts suitable for
installation in Python environments.

The initial package artifacts should include a source distribution and a
wheel.

## Linux Packages

Ariadne should support versioned packages for:

- Debian-based systems;
- Fedora;
- openSUSE.

The exact packaging tool and repository strategy remain pending.

## Windows Packages

Ariadne should support Windows installation through a package manager.

WinGet is the recommended first Windows package manager target because it is
the first-party Windows package manager. Chocolatey may be added later if
there is a clear maintenance benefit.

The exact installer format remains pending.

## Versioning

Every distributed artifact must identify the Ariadne version it contains.

Distribution artifacts must not claim support for checks, platforms, providers,
or integrations that have not been verified.

