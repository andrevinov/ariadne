# Ariadne Glossary

This glossary defines the product terms used by Ariadne's initial contract.

## Ariadne

A command-line flight fare tracker for collecting, comparing, and preserving
publicly available fare data for personal analysis.

## Fare search

A request containing the route, outbound date, and optional filters used to
query configured providers for flight fares.

## Minimum search request

A fare search request with origin, destination, and outbound date.

## Provider

A public website or service that may expose fare data, such as Google Flights,
Skyscanner, KAYAK, Momondo, Voopter, or Melhores Destinos.

## Provider-specific requirement

A parameter required by one provider but not by the global Ariadne product
contract. Such a requirement must be reported for that provider instead of
silently changing the global minimum search request.

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

