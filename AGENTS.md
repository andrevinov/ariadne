# Ariadne Repository Instructions

## 1. Purpose

This file defines the mandatory development rules for Ariadne.

Any AI agent, automated coding tool, or human contributor working in this
repository must follow these instructions.

This document is an operational contract. It defines:

- how changes must be planned;
- how the existing architecture must be respected;
- how tests and implementation must be produced;
- how project knowledge must be maintained;
- how changes must be explained to the maintainer;
- which artifacts are public and which must remain private.

Detailed explanations of this workflow may exist in private files, but this
document remains the authoritative source for repository behavior.

---

## 2. Language Policy

### 2.1 Public repository content

All public repository content must be written in English.

This includes:

- source code;
- class, function, method, variable, module, and package names;
- comments and docstrings;
- log messages;
- exception messages;
- CLI commands and help text;
- tests, test names, fixtures, and test data;
- public documentation;
- configuration comments;
- filenames and directory names;
- commit messages;
- pull request descriptions;
- issue templates;
- architecture decision records;
- this `AGENTS.md` file.

Tests may contain non-English data only when the behavior being tested
specifically requires multilingual content.

### 2.2 Private and ephemeral content

Private and ephemeral development material must be written in Portuguese.

Portuguese private and ephemeral content must use standard Portuguese
orthography, including accents, cedillas, and other required diacritics. Do not
transliterate Portuguese into ASCII-only text, except inside literal code,
commands, environment variables, file paths, identifiers, or quoted external
output.

This includes content stored under:

```text
.ai/
.project/
````

Examples:

* step-by-step implementation plans;
* task execution reports;
* private development tutorials;
* AI prompts;
* temporary architectural reasoning;
* investigation notes;
* personal roadmap notes;
* open questions;
* discarded alternatives;
* private traceability documents;
* debugging journals.

Interactive explanations and execution reports addressed to the maintainer
must also be written in Portuguese unless explicitly requested otherwise.

### 2.3 Separation rule

Public files must never depend on private files to explain how the software
works.

Private files may reference public documentation, but public documentation
must remain complete and understandable without access to `.ai/` or
`.project/`.

Do not copy private planning discussions into public documentation unless the
information represents a stable and relevant truth about the software.

---

## 3. Public and Private Project Knowledge

### 3.1 Public canonical documentation

Public documentation must describe the software itself, not the development
conversation that produced it.

The expected public structure is:

```text
docs/
├── product/
├── architecture/
├── decisions/
├── testing/
└── operations/
```

Public documentation may include:

* product behavior and supported use cases;
* domain terminology;
* current architecture;
* dependency rules;
* stable component responsibilities;
* important architectural decisions;
* testing strategy;
* installation and operation instructions.

Public documents must be updated only when the corresponding software truth
changes.

Do not create public documents merely to record that a task was executed.

### 3.2 Private AI workspace

The `.ai/` directory contains material used by AI agents during development.

Suggested structure:

```text
.ai/
├── development-handbook.md
├── plans/
├── reports/
├── investigations/
└── prompts/
```

Typical responsibilities:

* `development-handbook.md`: detailed tutorial explaining this workflow;
* `plans/`: plans prepared before implementation;
* `reports/`: task completion and code walkthrough reports;
* `investigations/`: technical research and debugging notes;
* `prompts/`: reusable private prompts and agent instructions.

### 3.3 Private project workspace

The `.project/` directory contains the maintainer's private project memory.

Suggested structure:

```text
.project/
├── roadmap.md
├── backlog.md
├── traceability.md
├── open-questions.md
└── notes/
```

The distinction is:

* `.ai/` contains AI operating material;
* `.project/` contains private project management and product reasoning.

Both directories must remain excluded from version control.

### 3.4 Authority order

When repository information conflicts, use this order:

1. explicit instructions from the maintainer for the current task;
2. this `AGENTS.md`;
3. accepted architectural decision records;
4. current canonical documentation;
5. established repository conventions;
6. existing implementation details.

Do not silently violate a higher-level rule because the existing code follows
an older convention.

When a requested change conflicts with the current architecture or product
contract, explain the conflict before implementing it.

---

## 4. Required Development Workflow

Every meaningful change must follow the workflow below.

Small and mechanical changes may use a shortened plan, but they must still
respect architecture, testing, verification, and reporting rules.

### 4.1 Preflight

Before modifying code, the agent must:

1. read this `AGENTS.md`;
2. read the relevant product documentation;
3. read the relevant architecture documentation;
4. inspect related architectural decisions;
5. search the repository for existing implementations;
6. inspect related tests and fixtures;
7. identify the current public components involved;
8. inspect relevant private plans or project notes when available;
9. review the current Git diff and repository status.

The agent must not assume that a new class, function, fixture, adapter, helper,
or file is necessary before searching for an existing equivalent.

### 4.2 Understand the requested behavior

Before designing the implementation, identify:

* the observable behavior being requested;
* the input and expected output;
* relevant business rules;
* expected failures;
* external systems involved;
* behavior that is explicitly outside the task;
* existing behavior that must remain unchanged.

When requirements are materially ambiguous, stop and ask for clarification.

Do not invent product behavior merely to make implementation easier.

### 4.3 Review architectural placement

Before creating tasks or writing code, determine:

* which module owns the requested behavior;
* which existing components should participate;
* which layer may depend on which other layer;
* where side effects belong;
* where external-system details must be isolated;
* whether a public interface or abstraction is actually necessary.

Every significant responsibility must have one canonical location.

Do not implement the same business rule independently in multiple adapters,
commands, services, or tests.

### 4.4 Prepare a private change plan

For meaningful changes, create or update a Portuguese plan under:

```text
.ai/plans/
```

The plan must state:

* objective;
* requested behavior;
* scope;
* behavior that will not be changed;
* existing components to reuse;
* files expected to change;
* files expected to be created;
* proposed architectural decisions;
* expected tests;
* relevant risks;
* verification commands;
* documentation that may require updates.

Every proposed new file or abstraction must include a reason for existing.

The plan is an implementation aid, not a public project artifact.

### 4.5 Implement a vertical slice

Prefer delivering observable behavior across the necessary layers instead of
implementing isolated technical layers.

Prefer:

```text
receive input
→ validate input
→ execute application behavior
→ interact with external boundaries
→ normalize the result
→ persist or return the result
```

Avoid task sequences such as:

```text
create all models
→ create all repositories
→ create all services
→ create all commands
```

unless the architecture clearly requires that order.

A completed slice should produce behavior that can be exercised and verified.

### 4.6 Implement tests and production code

Tests and production code may be developed within the same implementation
cycle.

There is no mandatory human approval gate between writing tests and writing
production code.

However, the semantic integrity of tests must be preserved.

The agent must not:

* weaken an assertion to make an implementation pass;
* delete a failing scenario without justification;
* replace a precise expectation with a vague one;
* change a business rule inside a test without explicit approval;
* mock away the behavior that the test is intended to verify;
* silently convert an integration test into a unit test;
* silently remove coverage of a failure path.

### 4.7 Verify the change

Before declaring a task complete, run the relevant available checks:

* focused tests;
* related integration tests;
* full test suite when practical;
* linting;
* formatting checks;
* static type checking;
* architecture or dependency checks;
* relevant CLI or application flows.

Also inspect:

* the complete Git diff;
* all modified tests;
* all newly created files;
* duplicated components;
* unused code;
* unrelated changes;
* temporary debugging artifacts;
* accidentally exposed private information.

Passing tests alone is not sufficient.

The implementation must also respect the architecture and the requested
behavior.

### 4.8 Update canonical documentation

Update public documentation only when the implementation changes a stable
truth about the software.

Examples:

* a new supported use case;
* a changed business rule;
* a new public command;
* a new stable component;
* a changed dependency rule;
* a significant architectural decision;
* a changed installation or operation procedure.

Do not publish:

* temporary plans;
* task-by-task narratives;
* prompt history;
* internal reasoning transcripts;
* debugging journals;
* discarded designs.

### 4.9 Produce a private execution report

After completing a meaningful change, create or update a Portuguese report
under:

```text
.ai/reports/
```

The report must explain:

* what behavior was delivered;
* which files changed;
* which tests were added or changed;
* which architectural decisions were made;
* what was verified;
* remaining limitations or risks;
* the execution path through the software;
* how the maintainer can manually observe the behavior.

---

## 5. Architecture Rules

The current architecture documentation defines the exact dependency rules.

Until more specific rules are documented, the following principles apply.

### 5.1 Isolate external systems

External systems must be isolated behind explicit boundaries.

This includes:

* Selenium or other browser automation tools;
* airline websites;
* flight comparison websites;
* Google Sheets;
* databases;
* filesystems;
* HTTP clients;
* operating-system services;
* schedulers;
* notification services.

Core application and domain code must not depend directly on Selenium,
browser page structures, Google APIs, or provider-specific HTML.

### 5.2 Isolate provider-specific behavior

Each airline, comparison service, or travel website must have isolated
provider-specific logic.

A change in one provider's page structure must not require changes to
unrelated providers or to core business rules.

Provider adapters may understand:

* selectors;
* navigation behavior;
* cookie dialogs;
* result-page structure;
* provider-specific terminology;
* provider-specific failure conditions.

Core application code should work with normalized concepts rather than raw
page elements.

### 5.3 Normalize data before use

Raw provider results must be converted into canonical application or domain
objects before they are compared, persisted, or exported.

Do not allow provider-specific dictionaries, page elements, or HTML fragments
to propagate through the application.

### 5.4 Keep side effects at boundaries

Business decisions should be testable without starting a browser, writing to
Google Sheets, or calling an external website.

Side effects must occur through explicit adapters or infrastructure
components.

### 5.5 Prefer composition

Prefer small components with explicit collaborators over deep inheritance
hierarchies.

Dependencies should normally be provided explicitly rather than created
inside business components.

Object creation and wiring should occur in dedicated composition roots or
application entry points.

### 5.6 Avoid speculative abstractions

Do not create an abstraction merely because multiple implementations may
exist someday.

Create an interface or protocol when at least one of these is true:

* the architecture requires dependency inversion;
* multiple implementations already exist;
* tests require a controlled external boundary;
* the external dependency must be isolated;
* the abstraction represents a stable domain capability.

### 5.7 Avoid circular knowledge

Lower-level domain and application components must not know about CLI,
Selenium, Google Sheets, or provider-specific modules.

Circular imports and circular architectural dependencies are prohibited.

---

## 6. Code Clarity and Documentation

Human understanding is a required project outcome.

Code should not merely work. It must make its responsibilities and execution
path understandable.

### 6.1 Docstrings

Public classes, public functions, important private components, and
non-obvious tests should contain short English docstrings.

Docstrings should explain:

* the component's responsibility;
* the behavior it provides;
* important guarantees;
* relevant architectural context;
* non-obvious failure behavior.

Avoid docstrings that only repeat the signature.

Poor:

```python
def search(request):
    """Searches using the request."""
```

Better:

```python
def search(request):
    """Collect and normalize confirmed fare offers from configured providers."""
```

### 6.2 Comments

Comments must explain intent, constraints, or reasoning.

Use comments for:

* non-obvious business rules;
* provider-specific behavior;
* temporary compatibility constraints;
* architectural boundaries;
* decisions that could otherwise look accidental.

Do not comment obvious operations.

Poor:

```python
price = price * 2  # Multiply the price by two.
```

Better:

```python
# Provider results are quoted per passenger, while FareOffer stores
# the confirmed total for the complete booking.
total_price = price_per_passenger * passenger_count
```

### 6.3 Names

Names must express domain meaning.

Avoid generic names such as:

* `Manager`;
* `Helper`;
* `Utils`;
* `Processor`;
* `Handler`;
* `Data`;
* `Thing`.

Use these names only when the responsibility is genuinely precise and
documented.

### 6.4 Types

Use type hints for public functions, component boundaries, domain objects,
ports, adapters, and non-trivial return values.

Avoid using unstructured dictionaries as long-lived internal models when a
stable object or typed structure is appropriate.

---

## 7. Execution-Flow Observability

Ariadne distinguishes technical debugging from execution narration.

Technical debugging answers questions such as:

* what exception was raised;
* which selector failed;
* what HTTP status was returned;
* how long an operation took.

Execution-flow narration answers questions such as:

* which application behavior started;
* which component was called;
* which object was created;
* which components were connected;
* which provider produced a result;
* why a result was accepted or rejected;
* where the application flow stopped.

### 7.1 Flow logger

Execution narration must use a dedicated logger:

```text
ariadne.flow
```

The implementation may later expose a custom `FLOW` logging level.

Until such a level is formally implemented, flow events may use the standard
debug level through the dedicated `ariadne.flow` logger.

Do not mix detailed provider debugging with application-flow narration.

### 7.2 Flow event style

Flow logs must be:

* written in English;
* structured and predictable;
* focused on meaningful lifecycle events;
* safe to display during development;
* useful for following the code path.

Prefer stable event names and key-value context.

Examples:

```text
FLOW | search_started | origin=FOR destination=MAO passengers=2
FLOW | provider_called | provider=google_flights
FLOW | browser_session_created | provider=google_flights
FLOW | fare_offer_created | airline=GOL total_brl=4064.00
FLOW | offer_rejected | reason=unconfirmed_round_trip_price
FLOW | provider_completed | provider=google_flights offers=4
FLOW | search_completed | confirmed_offers=7
```

### 7.3 Object lifecycle events

Record object lifecycle events when they help explain application behavior.

Useful examples:

* application request created;
* provider adapter selected;
* browser session created;
* normalized offer created;
* offer attached to a search result;
* repository called;
* exporter called;
* application service completed.

Do not log every local variable, loop iteration, property access, or trivial
object creation.

### 7.4 Sensitive data

Never log:

* passwords;
* authentication tokens;
* cookies;
* session identifiers;
* personal data;
* payment details;
* complete HTML pages;
* browser storage contents;
* private spreadsheet credentials.

URLs must be sanitized when they contain sensitive query parameters.

### 7.5 Logging must not control behavior

Application behavior must not depend on whether flow logging is enabled.

Flow logging is an observability mechanism, not part of the business logic.

---

## 8. Testing Rules

### 8.1 Test the appropriate boundary

Choose the test level according to the behavior, architectural boundary, and
risk being verified.

Use:

* unit tests for isolated business rules and deterministic transformations;
* integration tests for collaboration between internal components or with
  controlled infrastructure;
* contract tests for guarantees between ports, adapters, schemas, and external
  boundaries;
* browser tests for provider behavior that requires browser automation;
* end-to-end tests for critical complete application flows;
* regression tests for previously observed defects;
* security or performance tests when the associated risk justifies them.

Do not create a unit test merely because a new function exists.

Test observable behavior and architectural guarantees rather than private
implementation details.

---

### 8.2 Mandatory pytest markers

Every test must be classified with pytest markers that describe what the test
actually verifies.

Each test must have exactly one primary test-level marker:

```python
@pytest.mark.unit
@pytest.mark.integration
@pytest.mark.contract
@pytest.mark.browser
@pytest.mark.e2e
```

Additional purpose or execution markers may be combined with the primary
marker when appropriate:

```python
@pytest.mark.regression
@pytest.mark.security
@pytest.mark.performance
@pytest.mark.live
@pytest.mark.slow
```

Examples:

```python
@pytest.mark.unit
def test_rejects_offer_above_the_configured_result_limit():
    ...
```

```python
@pytest.mark.integration
@pytest.mark.regression
def test_persists_only_confirmed_offers_after_provider_collection():
    ...
```

```python
@pytest.mark.browser
@pytest.mark.live
@pytest.mark.slow
def test_collects_results_from_the_google_flights_search_page():
    ...
```

A marker must describe the test's real boundary, not its filename or directory.

For example:

* a test is not an integration test merely because it is stored under
  `tests/integration/`;
* a test that mocks every collaborator and verifies one isolated rule is
  normally a unit test;
* a test that verifies a real adapter against a controlled database,
  filesystem, browser page, or service boundary is normally an integration or
  contract test;
* a browser test is not necessarily end-to-end if it exercises only one
  provider adapter.

Primary markers may be applied at function, class, or module level when every
test in that scope shares the same classification.

Example:

```python
pytestmark = pytest.mark.unit
```

Do not apply a module-level marker when individual tests have different
boundaries.

---

### 8.3 Marker registration

All supported markers must be registered in the repository's pytest
configuration.

For example, in `pyproject.toml`:

```toml
[tool.pytest.ini_options]
markers = [
    "unit: verifies an isolated rule or component without real external boundaries",
    "integration: verifies collaboration between components or controlled infrastructure",
    "contract: verifies a stable agreement between ports, adapters, schemas, or external boundaries",
    "browser: verifies behavior that requires browser automation",
    "e2e: verifies a complete application flow through its public entry point",
    "regression: protects behavior related to a previously observed defect",
    "security: verifies a security requirement or threat control",
    "performance: verifies a measurable performance requirement",
    "live: accesses a real external system or website",
    "slow: identifies tests unsuitable for the default fast feedback loop",
]
```

Do not introduce unregistered markers.

When a new marker is necessary, document its meaning and register it before
using it.

---

### 8.4 Test docstrings

Test docstrings must explain the behavior, boundary, or guarantee protected by
the test.

They must not merely repeat the test name.

#### Unit tests

A unit-test docstring should identify the business rule or isolated behavior
when that rule is not already completely clear from the test name.

```python
@pytest.mark.unit
def test_rejects_offer_above_the_configured_result_limit():
    """Ensure the result limit prevents additional offers from being accepted."""
```

#### Integration tests

Every integration test must contain a docstring explaining:

* which components are being integrated;
* which collaboration is being verified;
* which external dependencies remain controlled or replaced.

```python
@pytest.mark.integration
def test_persists_confirmed_offers_after_provider_collection():
    """Integrate the search service with the offer repository using a temporary database."""
```

A more complex integration test may provide additional context:

```python
@pytest.mark.integration
def test_exports_normalized_offers_to_the_spreadsheet_gateway():
    """Integrate offer selection with the Sheets gateway while replacing the remote API."""
```

#### Contract tests

Every contract test must contain a docstring explaining:

* which parties participate in the contract;
* which interface or data guarantee is being verified;
* which application behavior or business rule depends on that guarantee.

```python
@pytest.mark.contract
def test_provider_adapter_returns_only_normalized_fare_offers():
    """Guarantee that provider adapters return FareOffer objects required by offer selection."""
```

```python
@pytest.mark.contract
def test_spreadsheet_gateway_preserves_the_required_column_order():
    """Guarantee the export schema required to append offers without corrupting existing data."""
```

#### Browser tests

Every browser test must explain:

* which provider behavior is exercised;
* whether the page is local, captured, simulated, or live;
* which page interaction or extraction behavior is protected.

```python
@pytest.mark.browser
def test_extracts_airline_and_total_price_from_a_local_results_page():
    """Exercise the GOL adapter against a controlled browser page without live access."""
```

#### End-to-end tests

Every end-to-end test must explain:

* the public entry point used;
* the complete flow exercised;
* the observable final result.

```python
@pytest.mark.e2e
def test_search_command_records_the_cheapest_confirmed_offers():
    """Run the CLI search flow from request creation through persisted results."""
```

#### Regression tests

Every regression test must explain the defect it prevents from returning.

```python
@pytest.mark.unit
@pytest.mark.regression
def test_does_not_count_the_same_offer_twice():
    """Prevent recurrence of duplicate offers consuming multiple result-limit positions."""
```

When an issue or defect identifier exists, include it in the docstring or test
metadata.

---

### 8.5 Selecting and combining markers

Primary markers identify the architectural level of the test.

Additional markers identify another relevant property.

Valid combinations include:

```python
@pytest.mark.unit
@pytest.mark.regression
```

```python
@pytest.mark.integration
@pytest.mark.slow
```

```python
@pytest.mark.browser
@pytest.mark.live
@pytest.mark.slow
```

```python
@pytest.mark.e2e
@pytest.mark.security
```

Do not apply multiple primary test-level markers merely to make a test appear
more comprehensive.

For example, avoid:

```python
@pytest.mark.unit
@pytest.mark.integration
def test_something():
    ...
```

When a test genuinely crosses several boundaries, classify it according to
the broadest boundary actually exercised or split it into separate tests with
clearer responsibilities.

---

### 8.6 External access

The default automated test suite must not depend on live airline or comparison
websites.

Tests that access a real external service must be:

* marked with `pytest.mark.live`;
* excluded from the default test suite;
* documented;
* safe to skip when browser dependencies, credentials, or connectivity are
  unavailable;
* written so that failure of the external provider is distinguishable from a
  product defect.

Browser automation alone does not require the `live` marker.

A browser test against a controlled local page may use only:

```python
@pytest.mark.browser
```

A browser test against a real provider should use:

```python
@pytest.mark.browser
@pytest.mark.live
@pytest.mark.slow
```

Provider parsers and browser adapters should use controlled HTML fixtures,
snapshots, or local test pages whenever practical.

---

### 8.7 Test readability

Tests must make the protected behavior understandable.

Test names should describe:

* the relevant condition;
* the action or behavior;
* the expected result.

Prefer:

```python
def test_rejects_offer_when_price_is_not_confirmed_for_round_trip():
    ...
```

Avoid:

```python
def test_offer_1():
    ...
```

Use Arrange, Act, and Assert separation when it improves readability, but do
not add ceremonial comments when the structure is already obvious.

Fixtures, builders, factories, and test helpers must use domain-oriented names.

Prefer:

```python
confirmed_round_trip_offer
two_adult_search_request
provider_with_three_offers
```

Avoid:

```python
data
fixture_1
mock_object
result_test
```

---

### 8.8 Test modification categories

The private execution report must classify every changed test under one of
these categories:

* new behavior coverage;
* regression coverage;
* refactoring without behavioral change;
* expected-behavior change.

An expected-behavior change includes:

* changing an expected result;
* removing a required scenario;
* accepting an input that was previously rejected;
* rejecting an input that was previously accepted;
* changing a business-rule boundary;
* weakening an assertion;
* changing the meaning of an existing test.

Any expected-behavior change requires explicit maintainer approval.

Moving a test, renaming it, extracting a fixture, or removing duplication does
not require approval when its protected behavior remains unchanged.

---

### 8.9 Test integrity

The agent must not:

* weaken an assertion to make production code pass;
* delete a failing scenario without justification;
* replace an exact expectation with a vague assertion;
* change expected behavior silently;
* mock the behavior that the test is supposed to verify;
* classify a test incorrectly to avoid running it;
* add `slow` or `live` merely to exclude a failing test from the default suite;
* catch unexpected exceptions inside a test only to prevent failure;
* rely exclusively on implementation details when observable behavior can be
  tested instead.

When a test fails because its expectation is no longer valid, stop and explain
the conflict before modifying the expectation.

---

### 8.10 Determinism

Default tests must not depend unintentionally on:

* the current date or time;
* network availability;
* live flight prices;
* provider page changes;
* global machine state;
* execution order;
* uncontrolled random values;
* the maintainer's browser profile;
* an existing local database;
* credentials stored outside the test environment.

Time, external providers, persistence, browser boundaries, and random values
must be controllable in tests.

Tests that intentionally depend on live or variable conditions must be marked
appropriately and must not be treated as deterministic verification of core
business rules.

---

### 8.11 Recommended execution groups

The repository should support focused execution by marker.

Examples:

```bash
pytest -m unit
pytest -m integration
pytest -m contract
pytest -m browser
pytest -m e2e
pytest -m regression
```

The default local and continuous-integration suite should exclude live tests:

```bash
pytest -m "not live"
```

A fast feedback command may exclude both live and slow tests:

```bash
pytest -m "not live and not slow"
```

Live tests must be executed intentionally:

```bash
pytest -m live
```

No successful live-provider execution should be required for ordinary unit,
integration, or contract verification.

---

## 9. Human Understanding as a Deliverable

A task is not complete merely because the code passes its tests.

The maintainer must be able to understand the behavior that was introduced.

Every meaningful private execution report must contain a code walkthrough in
Portuguese explaining:

1. where the behavior enters the application;
2. which component receives it first;
3. which objects are created;
4. how those objects collaborate;
5. where external systems are called;
6. how provider data is normalized;
7. where decisions are made;
8. where failures may occur;
9. which flow logs reveal each stage;
10. which tests protect the behavior.

Architectural decisions must be explained in terms of responsibilities and
trade-offs, not merely named.

Poor explanation:

```text
Foi usado dependency injection porque é uma boa prática.
```

Better explanation:

```text
O serviço recebe o adaptador pelo construtor para que a regra de seleção de
ofertas possa ser testada sem abrir um navegador e para impedir que o núcleo
da aplicação dependa diretamente do Selenium.
```

---

## 10. Definition of Done

A task may be considered complete only when all applicable conditions are
satisfied:

* the requested behavior is implemented;
* acceptance conditions are met;
* relevant tests pass;
* test changes have been reviewed through the Git diff;
* expected behavior was not silently changed;
* architecture rules are respected;
* external-system details remain isolated;
* flow logging exists where it improves execution understanding;
* public code and documentation are in English;
* private plans and reports are in Portuguese;
* relevant canonical documentation is updated;
* no unnecessary files or abstractions were created;
* no dead or duplicated code was introduced;
* no secrets or sensitive data were added;
* the private execution report explains the implementation;
* remaining limitations and risks are recorded.

---

## 11. Prohibited Actions

The agent must not:

* begin a meaningful implementation without inspecting existing code;
* create duplicate components without searching the repository;
* create public planning or execution-report documents;
* expose private prompts or project notes;
* place Portuguese content in public code or documentation;
* place English step-by-step reports in `.ai/` or `.project/`;
* weaken tests to make implementation pass;
* alter expected behavior without approval;
* silently change architecture;
* bypass CAPTCHA or access controls;
* implement mechanisms intended to evade provider security controls;
* fabricate prices, durations, airlines, availability, or fare conditions;
* treat teaser prices as confirmed offers;
* store sensitive browser data in logs or fixtures;
* leave temporary debugging code in the final diff;
* change unrelated files without justification;
* commit generated artifacts that do not belong in the repository;
* claim verification that was not actually performed.

---

## 12. Ariadne Product Integrity Rules

Ariadne must prioritize trustworthy results over result quantity.

A fare may be treated as confirmed only when it can be associated with:

* the requested origin and destination;
* the requested outbound date;
* the requested return date;
* the requested passenger count;
* a round-trip journey;
* a clearly displayed price;
* an identifiable source.

The application must preserve source provenance for every collected offer.

When information is unavailable, blocked, ambiguous, or unconfirmed, the
software must represent that uncertainty instead of inventing a value.

Provider failures are expected operational conditions.

One failing provider must not prevent unrelated providers from being queried
unless continuing would produce unsafe or invalid behavior.

CAPTCHA, authentication requirements, access denial, missing selectors, and
unexpected page structures must be reported as explicit provider failures.

---

## 13. Git Discipline

Before completing a task, inspect:

```bash
git status
git diff
git diff -- tests
```

Use the repository's configured formatter, linter, type checker, and test
commands.

Keep changes focused on the requested behavior.

Do not commit changes unless explicitly instructed to do so.

When commits are requested:

* commit messages must be in English;
* each commit should represent a coherent change;
* do not mix unrelated refactoring with behavioral changes;
* do not include `.ai/` or `.project/` content;
* do not claim tests passed unless they were executed successfully.

---

## 14. Final Response Requirements

After completing a development task, the response to the maintainer must be
written in Portuguese and include:

* behavior implemented;
* relevant architectural decisions;
* files created or modified;
* tests added or changed;
* checks actually executed;
* a brief execution walkthrough;
* remaining risks, limitations, or pending work.

Do not provide long unrelated suggestions.

Keep the report focused on the completed task and its immediate consequences.
