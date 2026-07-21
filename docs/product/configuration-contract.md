# Ariadne Configuration Contract

This document defines the initial product-level configuration contract. It
does not choose a settings library or implementation structure.

## Configuration Sources

Ariadne must read execution parameters from:

- CLI arguments;
- a TOML configuration file;
- environment variables for secrets and environment-specific values.

CLI flags override TOML values for the current run. Provider-specific TOML
values override global TOML values. Production secrets must come from
environment variables, not from repository files.

## Provider Selection Flags

Ariadne must support these provider-selection flags:

- `--all`: explicitly select all configured providers;
- `--include`: explicitly select only the listed providers;
- `--exclude`: explicitly select all configured providers except the listed
  providers.

Without these flags, Ariadne uses the default provider set: every configured
provider that can operate with the supplied input.

## Operational Limit Flags

Every operational limit must be configurable through a CLI flag for the current
run:

- `--max-concurrent-providers`;
- `--provider-delay-seconds`;
- `--provider-timeout-seconds`;
- `--retry-attempts`;
- `--retry-backoff-seconds`;
- `--retry-backoff-multiplier`;
- `--retry-backoff-max-seconds`;
- `--cooldown-seconds`;
- `--cache-ttl-seconds`;
- `--suspend-after-limit-failures`;
- `--suspension-seconds`;
- `--jitter-percent`.

## TOML Shape

The initial TOML configuration should support this shape:

```toml
[limits]
max_concurrent_providers = 1
provider_delay_seconds = 30
provider_timeout_seconds = 90
retry_attempts = 1
retry_backoff_seconds = 300
retry_backoff_multiplier = 2.0
retry_backoff_max_seconds = 3600
cooldown_seconds = 3600
cache_ttl_seconds = 3600
suspend_after_limit_failures = 3
suspension_seconds = 86400
jitter_percent = 20

[providers.google_flights]
enabled = true

[providers.google_flights.limits]
provider_delay_seconds = 30
cache_ttl_seconds = 3600

[output]
format = "table"
fields = [
  "provider",
  "origin",
  "destination",
  "outbound_date",
  "flight_date",
  "flight_time",
  "price",
  "currency",
  "cabin_class",
  "source",
  "provider_state",
  "collected_at",
]

[exports.google_sheets]
enabled = false
spreadsheet_id = ""
sheet_name = "Ariadne"
range = "Ariadne!A:Z"
value_input_option = "RAW"
insert_data_option = "INSERT_ROWS"
columns = []
credentials_env_var = "GOOGLE_APPLICATION_CREDENTIALS"

[network]
proxy_url_env_var = ""
vpn_required = false
vpn_interface = ""
expected_exit_ip = ""
```

## Output Fields

The `output.fields` list defines which values Ariadne returns in normal
application output and their order.

If `exports.google_sheets.columns` is empty, Google Sheets export must use the
same fields and order as `output.fields`.

## Google Sheets Configuration

The Google Sheets section must include:

- whether the export is enabled;
- spreadsheet ID;
- sheet name;
- append range;
- value input option;
- insert data option;
- optional export columns;
- the environment variable that points to Google credentials.

Secrets themselves must not be stored in TOML.

## Network Configuration

The network section may configure an explicit user-controlled proxy or VPN.
Proxy URLs that contain credentials must be supplied through environment
variables.

Network configuration must not enable automatic IP rotation, proxy pools,
residential proxies, or aggressive network-origin switching to bypass provider
blocks.

