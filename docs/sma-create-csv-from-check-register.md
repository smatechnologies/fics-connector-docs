---
title: SMACreateCSVFromCheckRegister
description: "Command-line reference for SMACreateCSVFromCheckRegister, the utility that builds a CSV file with chosen columns from an array in a saved FICS response."
sidebar_label: SMACreateCSVFromCheckRegister
tags:
  - Reference
  - Automation Engineer
  - Getting Started
---

# SMACreateCSVFromCheckRegister

## What is it?

SMACreateCSVFromCheckRegister is a command-line utility that reads a saved FICS web service response, finds an array inside it, and writes a CSV file with a column layout you choose. It is named for the check register response it was built for, and it works with any array in a response.

- Use this utility to turn a check register response into a CSV file for reconciliation or import
- Use this utility when you need specific columns, in a specific order, with your own headings
- Use this utility after SMAFICSConnector has saved a response with `-ResponseOutputFilename`

:::tip Example

```
SMACreateCSVFromCheckRegister.exe -ResponseFile=.\CheckRegister.txt -CaptureTag="Content|ResultSet|table1" -OutputCSVFile=.\checks.csv
```

:::

## Command line options

| Option | Required | Description |
|---|---|---|
| `-ResponseFile` | Yes | The saved response file to read. |
| `-CaptureTag` | Yes | The path to the array to read, using pipe (`\|`) separators for nested tags. |
| `-OutputCSVFile` | Yes | The CSV file to create. |
| `-HeaderColumns` | No | Pipe-separated column headings for the first row of the CSV file. |
| `-DataColumns` | No | Pipe-separated element names to read from each entry in the array, in the order the columns should appear. |
| `-VerboseLogging` | No | Writes additional detail to the log file, including the response file read and the hierarchy walked. |

If any of the three required options is missing, the utility stops and writes the missing option to the log, for example `ERROR: -OutputCSVFile must be specified`.

### Default columns

When `-HeaderColumns` and `-DataColumns` are not specified, the utility uses the check register layout it was built for:

| Setting | Default |
|---|---|
| `-HeaderColumns` | `Check Number\|Date\|Payee\|Status\|Amount` |
| `-DataColumns` | `_checkNumber\|_datePaid\|_payeeName\|None\|_paymentAmount` |

Supply both together if you supply either, so that the headings and the columns beneath them stay aligned. The utility does not check that the two lists are the same length.

:::note A column name that is not found is written as text
If a name in `-DataColumns` does not exist in an entry, the utility writes the name itself into that column rather than leaving it blank. That is how the default layout fills its **Status** column: `None` is not an element in the response, so every row reads `None`.

This is useful for a constant column, and it is worth knowing when a column comes out full of element names — that means the name does not match the response.
:::

## Exit codes

| Code | Meaning |
|---|---|
| `0` | The response was read and the CSV file was written. |
| `1` | A required option was missing, the response file could not be read, the tag hierarchy was not found, or the CSV file could not be written. |

Set the **Failure Criteria** for the OpCon job to **NE** (Not Equal) to `0`.

**Related topics:**

- [FICS Connector overview](./overview.md)
- [SMAFICSConnector](./sma-fics-connector.md)
- [SMAParseResponseFile](./sma-parse-response-file.md)

## FAQs

**Does this only work with a check register?**

No. The name reflects the response it was written for, but the utility reads any array in any saved response. Point `-CaptureTag` at the array and name the columns you want.

**How is this different from the CSV output of SMAParseResponseFile?**

SMAParseResponseFile converts an array to a CSV file using the elements it finds. This utility lets you choose which elements become columns, in what order, and what the headings say — and lets you include a constant column.

**One of my columns is full of element names instead of values. Why?**

The name in `-DataColumns` does not match an element in the response entries. The utility writes the name itself when it cannot find the element, so a column of repeated names means a misspelled or wrongly cased element name.

**Where does the log file go?**

Beside the utility, with a date, time and process identifier appended to the name, in the same way as the other FICS Connector utilities.

## Glossary

**`-CaptureTag`** — The pipe-separated path to the array within the response, in the same form used by SMAParseResponseFile.

**`-DataColumns`** — The pipe-separated list of element names read from each entry in the array. Order determines column order. A name that does not match an element is written as literal text.

**`-HeaderColumns`** — The pipe-separated list of headings written as the first row of the CSV file.

**Check register** — A FICS report listing issued checks with their numbers, dates, payees and amounts. The default column layout of this utility describes that report.
