# Ariadne Export Contract

This document defines the initial product contract for side exports.

## Export Role

Exports are side destinations for reviewing or consuming Ariadne data. They do
not replace SQLite as the canonical local history.

Google Sheets is the first known side export target.

## Google Sheets Client

Google Sheets and Google Drive integration should use Google's official Python
API client unless the architectural baseline records a stronger reason to use a
different supported client.

## Data Sent

Ariadne sends data rows only. It must not create, overwrite, or require header
rows in the destination sheet.

If the user configures `exports.google_sheets.columns`, Ariadne sends those
columns in the configured order.

If the user does not configure export columns, Ariadne sends the same fields
that the normal application output returns.

Unknown configured columns must be reported as configuration errors.

## Append Behavior

Ariadne must be able to identify the next writable row in the configured
spreadsheet range before sending data.

It may use Google Sheets append behavior, which appends values after the
detected table in a range, or it may read the configured range to find the next
available row.

Reading the sheet for append placement does not make Google Sheets a source of
truth for Ariadne history.

## Required Configuration

Google Sheets export requires:

- export enabled flag;
- spreadsheet ID;
- sheet name or append range;
- value input option;
- insert data option;
- credentials supplied through environment variables;
- optional column list.

## Failure Behavior

Export failures must not corrupt or redefine SQLite history.

Missing credentials, invalid spreadsheet ID, missing permissions, rejected
writes, quota failures, and unavailable Google APIs must be reported as export
failures.

Secrets must not be written to logs, fixtures, repository files, or normal
result output.

