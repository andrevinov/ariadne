# Ariadne Business Rules

This document defines product rules that must remain stable across
implementation choices.

## BR-001 - Minimum search input

A public fare search request must include origin airport, destination airport,
and outbound date.

For the initial contract, origin and destination must be provided as IATA
airport codes.

## BR-002 - Optional search fields

Return date, passenger count, cabin class, airline, price range, provider
selection, and provider-specific parameters are optional globally until a
provider-specific rule requires them for that provider.

Provider-specific requirements must be reported explicitly and must not
silently become global requirements. In the default provider set, a provider
with missing provider-specific parameters may be skipped. In an explicitly
selected provider set, missing provider-specific parameters must be reported as
input errors.

## BR-003 - Public fare data only

Ariadne may automate public fare searches and collect publicly displayed
objective fare facts. It must not access accounts, private areas, paywalled
content, non-public data, or restricted provider systems without
authorization.

## BR-004 - Confirmed offer requirements

A fare offer may be treated as confirmed only when it can be associated with:

- a clearly displayed price;
- a currency;
- a cabin class;
- a flight date;
- a flight time;
- an identifiable source.

## BR-005 - No fabricated values

Ariadne must represent missing, blocked, ambiguous, or unconfirmed information
as uncertain. It must not invent prices, durations, airlines, availability,
fare conditions, dates, times, or provider responses.

## BR-006 - Source provenance

Ariadne must preserve source provenance for every collected offer, including
confirmed, unconfirmed, ambiguous, and rejected offers.

## BR-007 - Collection history retention

Ariadne must preserve every collection within the scope of a search. It must
not keep only the best offer unless a future explicit product rule changes the
retention behavior.

## BR-008 - Isolated provider failures

A failing provider must not prevent unrelated enabled providers from being
queried unless continuing would produce unsafe or invalid behavior.

## BR-009 - Explicit provider states

Operational provider outcomes must be represented explicitly. The product must
distinguish successful collection, no results, CAPTCHA states, authentication
requirements, access denial, IP blocks, rate limits, throttling, cooldown,
unavailability, timeouts, unexpected page structure, ambiguous offers, and
unconfirmed offers.

## BR-010 - Responsible response to provider limits

When provider access is blocked, limited, throttled, or denied, Ariadne must
prefer reduced frequency, backoff, cooldown, cache reuse, or temporary
provider suspension.

Ariadne must not escalate traffic or force access when a provider reports or
imposes limits.

## BR-011 - `robots.txt`, terms, and provider restrictions

`robots.txt`, terms of use, anti-bot policies, CAPTCHA, and provider blocks
are product-relevant operational or contractual constraints. Ariadne must make
their effects observable through provider states, configuration, or user-facing
results instead of hiding them behind fabricated success.

Global conservative defaults must exist for frequency, backoff, cooldown,
cache, retry, and suspension behavior. Provider-specific values may override
those defaults.

## BR-012 - Configurable network egress limits

Ariadne may support an explicitly configured user-controlled proxy or VPN.

Ariadne must not use automatic IP rotation, proxy pools, residential proxies,
or aggressive network-origin switching to bypass explicit provider blocks,
bans, or rate limits.

## BR-013 - SQLite as first-version canonical storage

SQLite is the canonical local persistence target for the first version.

Other storage systems may be considered later, but they are not the primary
storage target for the initial product contract.

## BR-014 - Side exports are not primary persistence

Google Sheets or other side destinations may be used for display or
consumption of collected data. They must not be treated as Ariadne's primary
database in the first version.

## BR-015 - Stateless scheduled execution

Scheduled executions must be stateless. Each run must receive required inputs
from CLI parameters, configuration files, and environment variables rather
than relying on process memory from a previous run.

## BR-016 - Configuration sources

Ariadne must support CLI-provided execution parameters and a TOML
configuration file. Environment variables are the production source for
secrets and environment-specific settings.

Development environments may load `.env` files for local secrets and settings.

## BR-017 - Sensitive data handling

Ariadne must not write credentials, cookies, browser profiles, tokens, or
other sensitive authentication material to logs, fixtures, or repository
files.

## BR-018 - Personal, private, educational use

The initial product is intended for personal, private, and educational use.
Collected data must not be sold or used as the data source for a public
commercial product.

## BR-019 - Provider selection semantics

When no provider-selection flag is used, Ariadne must query every configured
provider that can operate with the supplied input. Providers that require
missing provider-specific parameters are skipped from that default provider set
without making the run fail.

When `--all`, `--include`, or `--exclude` is used, Ariadne must treat the
remaining provider set as explicitly selected. If any selected provider
requires a missing parameter, Ariadne must raise an informative input error
that names the provider and the missing parameter and tells the user to either
provide the parameter or remove the provider from the selected set.

## BR-020 - Runtime providers have no fixed priority

Google Flights is the first provider for development. That does not create a
runtime priority rule among providers. Ariadne may use deterministic ordering
for execution, logs, and display, but no provider is inherently more
authoritative because of its order.

## BR-021 - Google Sheets data-only export

When exporting to Google Sheets, Ariadne must send data rows only. It must not
create or rewrite header rows.

If the user configures export columns, Ariadne must send values in that order.
If the user does not configure columns, Ariadne must use the same fields
returned by the normal application output.

Ariadne must be able to identify where to append data in the configured sheet.

## BR-022 - Exact duplicate persistence behavior

Ariadne must retain every collection it performs. If a newly collected flight
or offer is literally identical to an existing stored record according to the
future deduplication key, Ariadne must not create a duplicate record for that
same fact. It must update a timestamp such as `updated_at`.

The exact deduplication key belongs to the persistence design.
