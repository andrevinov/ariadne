# Ariadne Product Contract

This document defines Ariadne's initial product behavior. It describes what
the software must do, which inputs and results matter, which failures are
expected, and which activities are outside the product scope.

Implementation structure, modules, classes, adapters, and library choices are
outside this contract. Those decisions belong to the architectural baseline.

## Product Goal

Ariadne helps technical users track publicly available flight fares across
configured travel providers, compare confirmed offers, and preserve collection
history for personal analysis.

The product prioritizes trustworthy, explainable results over the number of
results collected.

## Intended Use

Ariadne is intended for personal, private, and educational use by technical
users who can run a command-line application manually or through scheduled
automation such as cron.

Collected data must not be sold or used as the data source for a public
commercial product.

Users are responsible for understanding the laws, provider terms, and
operational risks that apply to their own use.

## Actors

### CLI user

The CLI user starts a fare search, provides request parameters, reviews the
result summary, and uses the collected history to support manual purchase
decisions.

### Scheduled execution

Scheduled execution starts Ariadne without interactive state, normally through
cron or an equivalent scheduler. It must receive all required execution inputs
from command-line parameters, configuration files, and environment variables.

### Travel provider

A travel provider is a public website or service that may expose fare data.
Candidate providers for exploration include Google Flights, Skyscanner, KAYAK,
Momondo, Voopter, and Melhores Destinos. The order and first supported
provider set are still pending delivery planning.

### Local operator

The local operator configures provider access, scheduling, local storage,
secrets, and optional network settings such as a user-controlled proxy or VPN.
For personal use, this may be the same person as the CLI user.

### Side consumer

A side consumer reads collected data exported from Ariadne, for example a
spreadsheet used for review. Google Sheets is a possible side destination, not
the primary storage system.

## Initial Product Capabilities

Ariadne must support these product capabilities in the initial product horizon:

- receive a fare search request from the CLI;
- support stateless manual or scheduled execution;
- consult enabled providers for public fare data;
- represent each provider's operational state explicitly;
- normalize provider data into comparable fare offers;
- distinguish confirmed offers from ambiguous or unconfirmed data;
- preserve source provenance for every collected offer;
- preserve every collection that is within the search scope;
- store the canonical local history in SQLite for the first version;
- optionally expose or export stored results to side systems after local
  persistence exists.

## Search Request Contract

A public search request must include, at minimum:

- origin;
- destination;
- outbound date.

The initial product contract may also accept optional fields:

- return date;
- passenger count;
- cabin class;
- airline;
- price range;
- provider selection;
- provider-specific parameters.

Provider-specific requirements must not silently become global product
requirements. If a provider requires a return date, passenger count, cabin
class, or another parameter that is optional globally, Ariadne must represent
that requirement for that provider instead of changing the minimum request for
all searches.

## Result Contract

Ariadne may collect candidate offers, confirmed offers, unconfirmed offers, and
provider states.

A fare offer may be treated as confirmed only when it can be associated with:

- a clearly displayed price;
- a currency;
- a cabin class;
- a flight date;
- a flight time;
- an identifiable source.

Source provenance must be preserved for every collected offer, including
offers that are ambiguous, incomplete, or rejected as unconfirmed.

Ariadne must represent missing, blocked, ambiguous, or unconfirmed data as
uncertain. It must not invent prices, durations, airlines, availability, fare
conditions, dates, times, or provider responses.

## Provider Operational States

Provider failures are expected operational conditions. A failing provider must
not prevent unrelated enabled providers from being queried unless continuing
would produce unsafe or invalid behavior.

Ariadne must be able to represent provider states such as:

- completed;
- no results;
- CAPTCHA challenge present;
- CAPTCHA unresolved;
- authentication required;
- access denied;
- IP blocked;
- rate limited;
- throttled;
- cooldown active;
- unavailable;
- timeout;
- missing selector;
- unexpected page structure;
- ambiguous offer;
- unconfirmed offer;
- cache reused;
- temporarily suspended.

The exact state names may be refined during implementation, but the product
must keep the distinction between successful collection, provider failure,
blocked access, operational throttling, ambiguous data, and unconfirmed data.

## Public Automation Policy

Ariadne may automate public fare searches that a user could perform manually.
It may handle CAPTCHA challenges when doing so is necessary to complete a
public consultation within accepted operational limits.

This does not authorize Ariadne to access accounts, private areas, paywalled
content, non-public data, provider systems, or restricted information without
authorization.

CAPTCHA, `robots.txt`, terms of use, anti-bot policies, rate limits, and access
blocks are product-relevant operational or contractual constraints. They must
be represented in provider behavior instead of being hidden behind fabricated
success.

When provider access is blocked or limited, Ariadne must prefer reduced
frequency, backoff, cooldown, cache reuse, or temporary provider suspension.
It must not escalate traffic or rotate network origins to force access.

## Network Configuration Policy

Ariadne may support an explicitly configured user-controlled proxy or VPN as a
runtime network setting. This capability exists for legitimate operation,
privacy, controlled environments, containers, cloud networks, or diagnosis.

Configurable network egress must not become automatic IP rotation, proxy
pools, residential proxy use, or aggressive network-origin switching to bypass
explicit provider blocks, bans, or rate limits.

## Persistence and History

SQLite is the canonical local persistence target for the first version.

Ariadne must preserve every collection within the scope of a search. It must
not preserve only the best offer unless a future, explicit product rule changes
the retention behavior.

The exact schema, deduplication policy, retention policy, and migration
strategy are outside this contract and must be defined later.

## Configuration and Execution

Ariadne's initial user interface is a CLI.

Manual and scheduled executions must be stateless. Each execution must receive
the required inputs from command-line parameters, a TOML configuration file,
and environment variables. Runtime history belongs in local persistence, not in
process memory carried between executions.

Development environments may use `.env` files for local secrets and settings.
Production secrets must be provided by environment variables from the server,
container, or deployment environment.

## Known External Integrations

Known product integrations are:

- public travel provider websites or services;
- browser automation or equivalent interaction tooling, still to be chosen;
- SQLite as first-version canonical local persistence;
- the operating system scheduler, such as cron;
- Docker or equivalent packaging, still to be planned;
- Google Sheets or Google Drive as possible side export destinations.

The product contract does not choose the browser automation tool, Google
integration library, storage schema, container strategy, or provider adapter
structure.

## Out of Scope

The initial product contract excludes:

- automatic ticket purchase;
- payment processing;
- storing payment information;
- accessing user accounts on travel providers;
- using credentials, cookies, sessions, or tokens that do not belong to the
  user;
- bypassing authentication, authorization, paywalls, account boundaries,
  private areas, or access controls protecting non-public content;
- exploiting vulnerabilities in third-party systems;
- modifying provider data;
- intentionally overloading providers or performing denial-of-service
  behavior;
- automatic IP rotation, proxy pools, residential proxies, or aggressive
  network-origin switching to bypass explicit blocks;
- selling collected data;
- using collected data as the source for a public commercial data product;
- guaranteeing provider availability;
- guaranteeing that displayed prices remain available for purchase;
- treating teaser, partial, estimated, or context-free prices as confirmed
  offers;
- storing sensitive browser profiles, cookies, tokens, or credentials in logs,
  fixtures, or repository files.

## Non-Blocking Open Items

These items do not block this product contract, but must be refined in later
phases:

- provider priority and first supported provider order;
- concrete frequency, backoff, cooldown, cache, retry, and suspension values
  per provider;
- exact TOML configuration format;
- exact export contract and Google Sheets format;
- browser automation tool choice;
- packaging and Docker strategy;
- SQLite schema, retention, and deduplication policy.

## Related Documents

- [Use Cases](use-cases.md)
- [Business Rules](business-rules.md)
- [Non-Functional Requirements](non-functional-requirements.md)
- [Glossary](glossary.md)

