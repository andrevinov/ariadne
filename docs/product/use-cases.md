# Ariadne Use Cases

This document defines the initial observable use cases for Ariadne.

## UC-001 - Run a fare search from the CLI

### Objective

Allow a technical user to request a public fare search from the command line.

### Primary actor

CLI user.

### Preconditions

- Ariadne is installed in a supported Python 3.14 environment.
- The user provides the minimum search fields.
- Any selected provider-specific settings are available.

### Input

Minimum input:

- origin airport as an IATA airport code;
- destination airport as an IATA airport code;
- outbound date.

Optional input:

- return date;
- passenger count;
- cabin class;
- airline;
- price range;
- enabled providers;
- path to a TOML configuration file;
- provider-specific parameters.

### Main flow

1. The user starts Ariadne from the CLI.
2. The user provides the minimum search fields.
3. Ariadne validates that the request is sufficient to describe a search.
4. Ariadne executes the search against enabled providers.
5. Ariadne reports confirmed offers, unconfirmed data, and provider states.
6. Ariadne preserves the collection history for later review.

### Alternative flows

- The user provides optional filters, and Ariadne applies them when the
  provider supports the requested fields.
- The user provides no provider-selection flag, and Ariadne queries every
  configured provider that can operate with the supplied input.
- The user provides no provider-selection flag, and Ariadne skips providers
  that need missing provider-specific parameters.
- The user uses `--all`, `--include`, or `--exclude`, and Ariadne validates
  every explicitly selected provider before collection starts.

### Expected failures

- A required field is missing.
- A date or filter has an invalid format.
- No provider is enabled.
- A provider-specific required field is missing for an explicitly selected
  provider.

### Related rules

- BR-001
- BR-002
- BR-019
- BR-015
- BR-016

### Acceptance criteria

- AC-001: A request with origin IATA airport code, destination IATA airport
  code, and outbound date is a valid minimum search request.
- AC-002: Return date, passenger count, cabin class, airline, price range, and
  provider-specific parameters are optional globally.
- AC-003: Without provider-selection flags, a provider that requires a missing
  provider-specific parameter is skipped from the default provider set.
- AC-004: Invalid input is reported as an input failure, not as a provider
  failure.
- AC-023: With `--all`, `--include`, or `--exclude`, a selected provider that
  requires a missing parameter causes an informative input error that names the
  provider and missing parameter.

### Out of scope

- Automatic ticket purchase.
- Interactive graphical user interfaces.
- Provider account login.

## UC-002 - Run a stateless scheduled collection

### Objective

Allow Ariadne to run from cron or an equivalent scheduler without relying on
process memory from previous executions.

### Primary actor

Scheduled execution.

### Preconditions

- The scheduler can run the Ariadne CLI.
- The execution environment provides the required command-line parameters,
  configuration file, and environment variables.
- Local persistence is available.

### Input

- CLI parameters for the execution.
- TOML configuration values.
- Environment variables for secrets and environment-specific settings.

### Main flow

1. The scheduler starts Ariadne.
2. Ariadne reads the provided execution inputs.
3. Ariadne performs the requested collection.
4. Ariadne writes collection history to local persistence.
5. Ariadne exits with an observable result.

### Alternative flows

- Optional configuration values are omitted, and Ariadne uses documented
  defaults where available.
- A provider is in cooldown or temporarily suspended, and Ariadne reports that
  provider state without treating the whole execution as failed.

### Expected failures

- Required CLI input is missing.
- The configuration file is unavailable or invalid.
- A required environment variable is missing.
- Local persistence is unavailable.

### Related rules

- BR-007
- BR-013
- BR-015
- BR-016
- BR-020

### Acceptance criteria

- AC-005: A scheduled run does not depend on process memory from a previous
  run.
- AC-006: Runtime history is read from and written to local persistence, not
  stored only in memory.
- AC-007: Missing configuration is reported as an execution input failure.
- AC-024: CLI flags can override operational limits from TOML for the current
  run.

### Out of scope

- Implementing a scheduler.
- Defining a Docker image.

## UC-003 - Collect public fare data from enabled providers

### Objective

Collect candidate fare data from enabled public providers for a valid search
request.

### Primary actor

Scheduled execution or CLI user.

### Preconditions

- The search request is valid.
- At least one provider is enabled.
- Provider access remains within the product's public automation policy.

### Input

- Valid search request.
- Enabled provider list.
- Provider-specific operational settings.

### Main flow

1. Ariadne receives a valid search request.
2. Ariadne attempts collection from each enabled provider.
3. Each provider returns candidate fare data, no data, or an operational
   state.
4. Ariadne keeps provider outcomes separate.
5. Ariadne continues with unrelated enabled providers when one provider fails,
   unless continuing would be unsafe or invalid.

### Alternative flows

- A provider returns no results for the search.
- A provider presents a CAPTCHA challenge during a public consultation.
- A provider is rate limited, throttled, blocked, or unavailable.
- A provider is skipped because cooldown is active.
- Cached data is reused according to the configured policy.
- No provider-selection flag was used, and a provider is skipped because the
  supplied input lacks provider-specific required parameters.

### Expected failures

- CAPTCHA unresolved.
- Authentication required.
- Access denied.
- IP blocked.
- Rate limited.
- Throttled.
- Timeout.
- Unexpected page structure.
- Missing selector.

### Related rules

- BR-003
- BR-008
- BR-009
- BR-010
- BR-011
- BR-012
- BR-019
- BR-020

### Acceptance criteria

- AC-008: A provider failure is recorded without preventing unrelated enabled
  providers from being queried.
- AC-009: CAPTCHA, authentication requirements, access denial, rate limits, IP
  blocks, throttling, timeouts, and unexpected pages are represented as
  provider states.
- AC-010: Ariadne does not access accounts, private areas, non-public data, or
  restricted provider systems.
- AC-011: Ariadne responds to blocked or limited provider access with reduced
  frequency, backoff, cooldown, cache reuse, or temporary suspension instead
  of traffic escalation or network-origin rotation.
- AC-025: Google Flights is the first provider used for development, but that
  does not make it the highest-priority runtime provider.

### Out of scope

- Bypassing authentication or authorization.
- Exploiting provider vulnerabilities.
- Forcing access after explicit provider blocks.

## UC-004 - Normalize and classify provider results

### Objective

Turn provider data into comparable results and distinguish confirmed offers
from unconfirmed, ambiguous, or rejected data.

### Primary actor

CLI user or scheduled execution.

### Preconditions

- Provider collection returned candidate data or provider states.

### Input

- Candidate provider data.
- Provider state information.
- Search request context.

### Main flow

1. Ariadne reads candidate data from a provider outcome.
2. Ariadne associates each candidate with its source.
3. Ariadne identifies whether the candidate has the required confirmation
   fields.
4. Ariadne classifies the candidate as confirmed, unconfirmed, ambiguous, or
   rejected.
5. Ariadne reports uncertainty instead of inventing missing values.

### Alternative flows

- A result has price but no currency.
- A result has a teaser price without enough context.
- A result has flight information but no flight time.
- A provider returns data that conflicts with the search request.

### Expected failures

- Unknown currency.
- Missing price.
- Missing cabin class.
- Missing flight date.
- Missing flight time.
- Missing source.
- Ambiguous fare context.

### Related rules

- BR-004
- BR-005
- BR-006
- BR-009

### Acceptance criteria

- AC-012: A confirmed offer has price, currency, cabin class, flight date,
  flight time, and identifiable source.
- AC-013: Teaser, partial, estimated, or context-free prices are not treated
  as confirmed offers.
- AC-014: Missing data remains explicit; Ariadne does not fabricate values to
  complete an offer.
- AC-015: Source provenance is preserved for every collected offer.

### Out of scope

- Defining the internal normalized data structure.
- Choosing parsing libraries or provider selectors.

## UC-005 - Preserve local collection history

### Objective

Preserve all collections within the search scope in local SQLite persistence.

### Primary actor

CLI user or scheduled execution.

### Preconditions

- A collection run has provider outcomes to preserve.
- Local persistence is available.

### Input

- Search request context.
- Provider outcomes.
- Confirmed offers.
- Unconfirmed or ambiguous collected data.
- Provider operational states.

### Main flow

1. Ariadne receives the collection outcome.
2. Ariadne preserves all collected records within the search scope.
3. Ariadne preserves provider states associated with the run.
4. Ariadne makes the local history available for later comparison.

### Alternative flows

- No confirmed offers were found, but provider states and unconfirmed outcomes
  still need to be preserved.
- Some providers failed while others returned data.

### Expected failures

- Local persistence unavailable.
- Data cannot be preserved consistently.

### Related rules

- BR-006
- BR-007
- BR-013

### Acceptance criteria

- AC-016: Ariadne preserves every collection within the search scope.
- AC-017: Ariadne does not preserve only the best offer unless a future
  explicit product rule changes retention behavior.
- AC-018: Provider states are preserved with enough context to explain the
  collection outcome.
- AC-019: SQLite is the canonical local persistence target for the first
  version.

### Out of scope

- Defining database schema.
- Defining retention and deduplication policy.
- Supporting non-SQLite primary storage in the first version.

## UC-006 - Review or export collected results

### Objective

Allow collected history to be reviewed locally and, later, exposed to side
systems such as Google Sheets.

### Primary actor

CLI user or side consumer.

### Preconditions

- Local collection history exists.
- Any side export destination is configured.

### Input

- Stored collection history.
- Optional export destination settings.

### Main flow

1. Ariadne reads stored collection history.
2. Ariadne presents or exports results for review.
3. Ariadne keeps SQLite as the canonical local storage.
4. Ariadne treats side destinations as consumers, not as the primary database.

### Alternative flows

- No export destination is configured, and results remain available locally.
- Google Sheets is configured as a side display destination.
- Google Sheets columns are configured, and Ariadne sends values in that
  configured column order.
- Google Sheets columns are not configured, and Ariadne uses the same fields
  returned by the normal application output.

### Expected failures

- Export destination unavailable.
- Export credentials missing.
- Side destination rejects the write.

### Related rules

- BR-013
- BR-014
- BR-017
- BR-021

### Acceptance criteria

- AC-020: Google Sheets, if supported, is a side display or consumption
  destination, not Ariadne's canonical database.
- AC-021: Export failures do not redefine the persisted local history.
- AC-022: Secrets required for exports are not written to logs or repository
  files.
- AC-026: Google Sheets export sends data rows only and does not create header
  rows.
- AC-027: Ariadne can determine the next writable row before sending data to a
  configured sheet.

### Out of scope

- Implementing export credentials.
- Making Google Sheets part of the first executable flow unless a later
  delivery map includes it.
