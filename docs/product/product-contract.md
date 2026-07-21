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
Momondo, Voopter, and Melhores Destinos.

Initial development must start with Google Flights because it is expected to
have broad fare coverage. This is a development starting point, not a runtime
provider priority rule.

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

- origin airport as an IATA airport code;
- destination airport as an IATA airport code;
- outbound date.

The initial product contract may also accept optional fields:

- return date;
- passenger count;
- cabin class;
- airline;
- price range;
- provider selection;
- provider-specific parameters.

Origin and destination use IATA airport codes in the initial contract. Airports
without IATA codes are outside the initial minimum search input and may be
handled later as provider-specific or extended location support.

Provider-specific requirements must not silently become global product
requirements. If a provider requires a return date, passenger count, cabin
class, or another parameter that is optional globally, Ariadne must treat that
requirement as provider-specific.

## Provider Selection Contract

When a search runs without provider-selection flags, Ariadne must attempt every
configured provider that can produce values from the supplied input. Providers
that need missing provider-specific parameters are skipped from the default
provider set without making the whole run fail.

Provider selection changes when the user uses `--all`, `--include`, or
`--exclude`:

- `--all` explicitly selects all configured providers;
- `--include` explicitly selects the listed providers;
- `--exclude` explicitly selects all configured providers except the excluded
  providers.

When providers are explicitly selected, Ariadne must validate that the supplied
input is sufficient for every selected provider. If a selected provider needs a
missing parameter, Ariadne must raise an informative input error naming the
provider, naming the missing parameter, and explaining that the user must
either provide that parameter or remove the provider from the selected set.

There is no fixed runtime priority among providers. Provider ordering may be
used for deterministic execution, logs, or display, but it must not imply that
one provider is more authoritative than another.

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

## Default Operational Limits

Ariadne must start with conservative global operational defaults. Every default
must be overridable for one run through CLI flags, globally through the TOML
configuration file, and per provider through the TOML configuration file.

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

These values are intentionally conservative defaults, not provider-specific
final tuning.

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

The initial persistence model should follow the product glossary closely. Core
stored concepts are expected to include providers, search requests, provider
states, candidate offers, confirmed offers, flights, collections, exports, and
timestamps.

Ariadne must retain every collection it performs. When an incoming flight or
offer record is literally identical to an existing stored record according to
the future deduplication key, Ariadne must not create a duplicate row for that
same fact. It must update a timestamp such as `updated_at` while preserving the
historical collection context.

The exact schema, indexes, migration strategy, and deduplication key are still
implementation details for the architectural baseline and persistence slice.

## Configuration and Execution

Ariadne's initial user interface is a CLI.

Manual and scheduled executions must be stateless. Each execution must receive
the required inputs from command-line parameters, a TOML configuration file,
and environment variables. Runtime history belongs in local persistence, not in
process memory carried between executions.

CLI flags must be able to override the configured operational limits for the
current run. The TOML file must support both global defaults and provider-
specific overrides.

Development environments may use `.env` files for local secrets and settings.
Production secrets must be provided by environment variables from the server,
container, or deployment environment.

## Known External Integrations

Known product integrations are:

- public travel provider websites or services;
- browser automation or equivalent interaction tooling, with Playwright
  recommended for initial evaluation and final confirmation pending;
- SQLite as first-version canonical local persistence;
- the operating system scheduler, such as cron;
- Docker for deployable runtime images;
- versioned package distribution for Linux and Windows;
- Google Sheets or Google Drive as side export destinations through Google's
  official Python API client.

The product contract does not define provider adapter structure, database
schema, container implementation, distribution build tooling, or final browser
automation architecture.

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

## Refined Items for Later Phases

These items are now partially constrained by product decisions, but still need
architectural or delivery detail:

- Google Flights is the first development provider, but the full provider
  delivery order is still a delivery-map decision;
- operational defaults exist, but provider-specific tuning must be refined
  during provider preflight;
- the initial TOML contract is documented, but parser and settings-library
  choices belong to implementation;
- the Google Sheets export contract is documented, but authentication flow and
  API details belong to implementation;
- Playwright is the recommended initial browser automation tool, but final
  confirmation belongs to the architectural baseline;
- Docker, Linux packages, and Windows packages are target distribution forms,
  but build tooling remains pending;
- SQLite retention and exact-duplicate behavior are constrained here, but the
  physical schema and migration strategy remain pending.

## Related Documents

- [Use Cases](use-cases.md)
- [Business Rules](business-rules.md)
- [Non-Functional Requirements](non-functional-requirements.md)
- [Glossary](glossary.md)
- [Configuration Contract](configuration-contract.md)
- [Export Contract](export-contract.md)
- [Distribution Targets](distribution-targets.md)
