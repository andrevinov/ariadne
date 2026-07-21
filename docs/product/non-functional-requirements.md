# Ariadne Non-Functional Requirements

This document defines product quality requirements for Ariadne's initial
product horizon.

## NFR-001 - Trustworthy results

Ariadne must prefer truthful uncertainty over result quantity. Ambiguous,
blocked, partial, or unconfirmed information must be visible as such.

## NFR-002 - Provider failure isolation

One provider's operational failure must not stop unrelated enabled providers
unless continuing would produce unsafe or invalid behavior.

## NFR-003 - Bounded provider interaction

Provider interaction must support operational limits such as frequency,
backoff, cooldown, cache reuse, and temporary suspension.

Initial global defaults:

- maximum concurrent providers: `1`;
- delay between provider attempts in one run: `30` seconds;
- provider timeout: `90` seconds;
- retry attempts after transient failures: `1`;
- initial retry backoff: `300` seconds;
- retry backoff multiplier: `2.0`;
- maximum retry backoff: `3600` seconds;
- cooldown after rate limit, throttling, IP block, or access denial:
  `3600` seconds;
- cache TTL for equivalent search requests: `3600` seconds;
- temporary suspension after consecutive limit failures: `3` failures;
- temporary suspension duration: `86400` seconds;
- scheduling jitter: `20` percent.

Each value must be configurable through a CLI flag for one run, through global
TOML configuration, and through provider-specific TOML configuration.

## NFR-004 - No traffic escalation

Ariadne must not respond to provider blocks, bans, rate limits, or throttling
by increasing traffic volume or rotating network origins to force access.

## NFR-005 - Public-data boundary

Ariadne must remain within public fare search flows. It must not access
private areas, accounts, non-public content, or restricted systems without
authorization.

## NFR-006 - Secret protection

Secrets, credentials, cookies, browser profiles, tokens, and export
credentials must not be written to logs, test fixtures, repository files, or
normal result output.

## NFR-007 - Stateless operation

Manual and scheduled executions must be stateless from the process
perspective. Durable history belongs in local persistence, not in process
memory carried between runs.

## NFR-008 - Cron-friendly operation

Ariadne must be operable from a command line in a way that can be scheduled by
cron or an equivalent scheduler.

## NFR-009 - Local-first persistence

The first version must preserve canonical history locally in SQLite. Side
exports must not replace local history.

## NFR-010 - Observable provider outcomes

Provider outcomes must be observable enough for a user to distinguish success,
no results, blocked access, rate limiting, CAPTCHA, authentication
requirements, unavailable providers, unexpected pages, ambiguous data, and
unconfirmed offers.

## NFR-011 - Configuration clarity

Runtime behavior must be explainable from command-line parameters, TOML
configuration, and environment variables. Missing or invalid configuration
must be reported as an input or configuration failure.

## NFR-012 - Python compatibility

Ariadne targets the Python 3.14 series for the initial implementation.

## NFR-013 - Personal-use operational posture

Ariadne must be suitable for personal, private, and educational use. It must
avoid behavior that deliberately overloads providers or attempts to turn
provider restrictions into forced access.

## NFR-014 - Auditability of collected data

Collected results must preserve enough context to explain where the data came
from, which search produced it, whether it was confirmed, and which provider
state affected it.

## NFR-015 - Export safety

Export destinations must be treated as side consumers. An export failure must
not corrupt or redefine the canonical local history.

## NFR-016 - Distribution targets

Ariadne must support Docker-based deployment and versioned package
distribution for distinct Linux and Windows environments.

Initial distribution targets are:

- a Docker image;
- Python package artifacts suitable for versioned installation;
- Debian package distribution;
- Fedora package distribution;
- openSUSE package distribution;
- Windows package distribution.

WinGet is the recommended first Windows package manager target. Chocolatey may
be added later if there is a clear maintenance benefit.

## NFR-017 - Google Sheets append safety

Google Sheets export must append data rows without writing headers. Ariadne
must identify the next writable row through the configured sheet range or the
Google Sheets append behavior before sending data.

## NFR-018 - Initial airport identity

Origin and destination identity must initially use IATA airport codes. Airports
without IATA codes are outside the minimum initial search contract.
