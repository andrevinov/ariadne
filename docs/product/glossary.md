# Ariadne Glossary

This glossary defines the product terms used by Ariadne's initial contract.

## Ariadne

A command-line flight fare tracker for collecting, comparing, and preserving
publicly available fare data for personal analysis.

## Fare search

A request containing the route, outbound date, and optional filters used to
query configured providers for flight fares.

## Minimum search request

A fare search request with origin IATA airport code, destination IATA airport
code, and outbound date.

## IATA airport code

A three-letter airport identifier used by the initial Ariadne search contract
for origin and destination airports.

Small airports outside major reservation systems may not have IATA codes.
Those airports are outside the initial minimum search input.

## Provider

A public website or service that may expose fare data, such as Google Flights,
Skyscanner, KAYAK, Momondo, Voopter, or Melhores Destinos.

## Provider-specific requirement

A parameter required by one provider but not by the global Ariadne product
contract.

Such a requirement must not silently change the global minimum search request.
In the default provider set, it may make the provider ineligible for that run.
In an explicitly selected provider set, it must be reported as an input error.

## Default provider set

The configured providers that can operate with the input supplied for a run
when the user does not use provider-selection flags.

## Explicitly selected provider set

The provider set produced when the user uses `--all`, `--include`, or
`--exclude`. Ariadne must validate that every provider in this set has the
parameters it requires before collection starts.

## Google Flights

A public travel search provider. Ariadne must start provider development with
Google Flights because it is expected to have broad fare coverage. This does
not make Google Flights the highest-priority provider at runtime.

## Candidate offer

Fare data collected from a provider before Ariadne determines whether it is a
confirmed offer.

## Confirmed offer

A fare offer associated with a clearly displayed price, currency, cabin class,
flight date, flight time, and identifiable source.

Teaser prices, partial prices, estimates, or values without enough context are
not confirmed offers.

## Unconfirmed offer

A collected offer that lacks one or more required confirmation fields or lacks
enough context to support comparison.

## Ambiguous offer

A collected offer whose meaning cannot be determined reliably, for example a
price that may not match the requested route, passenger count, class, or flight
time.

## Source provenance

The provider and collection context from which a collected offer came. Source
provenance must be preserved for every collected offer.

## Public fare data

Objective fare facts displayed publicly, such as price, currency, route,
dates, times, stops, airline, cabin class, and objective fare conditions.

Public fare data does not include private account information, payment data,
restricted content, session data, provider credentials, creative page content,
or complete provider HTML.

## System intrusion

Unauthorized access to a system, account, session, private area, restricted
information, or non-public data, especially through vulnerabilities,
credentials that do not belong to the user, session hijacking, cookies, or
tokens.

Ariadne prohibits system intrusion.

## CAPTCHA challenge

A provider challenge that may appear during public consultation. Ariadne may
handle CAPTCHA challenges when they are necessary to complete a public fare
search within accepted operational limits.

An unresolved CAPTCHA must be represented as a provider state.

## Provider state

The observable result of interacting with a provider. Examples include
completed, no results, CAPTCHA unresolved, authentication required, access
denied, IP blocked, rate limited, throttled, cooldown active, unavailable,
timeout, unexpected page structure, ambiguous offer, and unconfirmed offer.

## Rate limit

A provider condition indicating that Ariadne should reduce or stop requests
for a period instead of trying to force continued access.

## Throttling

A provider condition where responses are delayed, degraded, or limited because
the provider is constraining access.

## Backoff

Reducing request attempts after failures or provider limits. Concrete backoff
values are provider-specific and remain pending.

## Cooldown

A period during which a provider is skipped or queried less frequently after a
block, limit, throttling condition, or other operational constraint.

## Cache reuse

Using previously collected data when provider access should be reduced or
avoided. Cache reuse must remain distinguishable from a fresh successful
collection.

## Temporary provider suspension

Skipping a provider for a configured period after repeated or serious
operational failures, limits, or blocks.

## Operational defaults

The conservative global values Ariadne uses for provider frequency, backoff,
cooldown, cache, retry, and suspension behavior when no CLI or provider-specific
configuration overrides them.

## Configurable network egress

An explicit user-controlled runtime setting for proxy or VPN use. It may be
used for legitimate operation, privacy, controlled environments, containers,
cloud networks, or diagnosis.

It does not mean automatic IP rotation, proxy pools, residential proxies, or
aggressive network-origin switching to bypass provider blocks.

## Stateless execution

An execution model where each Ariadne run receives the necessary inputs from
CLI parameters, configuration files, and environment variables. Durable
history is stored locally, not kept only in process memory.

## CLI

The command-line interface used to start Ariadne manually or through a
scheduler.

## TOML configuration

A configuration file format Ariadne is expected to support for runtime
settings.

## SQLite

The canonical local persistence target for Ariadne's first version.

## Side export

An optional destination that consumes Ariadne data, such as Google Sheets.
Side exports are not the canonical database for the first version.

## Google Sheets export

A side export that appends Ariadne data rows to a configured Google Sheets
spreadsheet. It does not create header rows.

## Exact duplicate

A newly collected flight or offer that is literally identical to an existing
stored record according to Ariadne's future deduplication key.

## `updated_at`

A timestamp that may be updated when Ariadne sees an exact duplicate instead
of inserting a duplicate record for the same fact.
