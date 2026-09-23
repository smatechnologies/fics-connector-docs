---
title: SMABuildListFromJSONArray
description: "Command-line reference for SMABuildListFromJSONArray, the utility that builds a list file from one element of every entry in a JSON array in a saved FICS response."
sidebar_label: SMABuildListFromJSONArray
tags:
  - Reference
  - Automation Engineer
  - Getting Started
---

# SMABuildListFromJSONArray

## What is it?

SMABuildListFromJSONArray is a command-line utility that reads a saved FICS web service response, finds a JSON array inside it, and writes one named element from every entry in that array to a list file.

- Use this utility when a FICS operation returns an array and a later job needs every value of one field from it, such as a list of loan identifiers
- Use this utility after SMAFICSConnector has saved a response with `-ResponseOutputFilename`

Where SMAParseResponseFile extracts a single tag value, this utility extracts the same element from every entry in an array and collects the results into one file.

:::tip Example

```
SMABuildListFromJSONArray.exe -ResponseFile=.\LoanReport.txt -CaptureTag="Content|ResultSet|table1" -ElementToCapture="LoanId" -TagValueFilename=.\loanlist.txt
```

:::

## Command line options

| Option | Required | Description |
|---|---|---|
| `-ResponseFile` | Yes | The saved response file to read. |
| `-CaptureTag` | Yes | The path to the array to read, using pipe (`\|`) separators for nested tags. |
| `-ElementToCapture` | Yes | The name of the element to take from each entry in the array. |
| `-TagValueFilename` | Yes | The file to create containing the collected values. |
| `-ConfigFile` | No | An alternative configuration file to use. |
| `-VerboseLogging` | No | Writes additional detail to the log file, including the response file read and the hierarchy walked. |

If any of the four required options is missing, the utility stops and writes the missing option to the log, for example `ERROR: -CaptureTag must be specified`.

:::note
`-CaptureTag` uses the same pipe-separated form as SMAParseResponseFile. Refer to [SMAParseResponseFile](./sma-parse-response-file.md) for a worked example of addressing a nested tag.
:::

## Output format

Each captured value is written on its own line. Every line except the last ends with a comma, which makes the file usable as a list:

```
833,
4038,
4218
```

Values are written according to their type in the response. A date is quoted and reformatted; a numeric value is written as a number. Entries where the named element is missing or empty are skipped rather than written as blank lines.

## Exit codes

| Code | Meaning |
|---|---|
| `0` | The response was read and the list file was written. |
| `1` | A required option was missing, the response file could not be read, or the tag hierarchy was not found. |

Set the **Failure Criteria** for the OpCon job to **NE** (Not Equal) to `0`.

**Related topics:**

- [FICS Connector overview](./overview.md)
- [SMAFICSConnector](./sma-fics-connector.md)
- [SMAParseResponseFile](./sma-parse-response-file.md)

## FAQs

**How is this different from SMAParseResponseFile?**

SMAParseResponseFile captures a single tag value, or converts an array to a CSV file. SMABuildListFromJSONArray takes one element from every entry in an array and writes the values as a list. Use it when a downstream job needs the set of values rather than a table.

**What do I point `-CaptureTag` at, the array or an entry?**

Point it at the array itself. The utility walks the hierarchy to the array and then iterates the entries within it, taking `-ElementToCapture` from each one.

**Why does the last line have no comma?**

So that the file can be used directly as a list without a trailing separator. Every preceding line is comma-terminated.

**Where does the log file go?**

Beside the utility, with a date, time and process identifier appended to the name, in the same way as the other FICS Connector utilities.

## Glossary

**Array** — A JSON structure in a FICS response holding a repeated set of entries, such as one entry per loan or per transaction.

**`-ElementToCapture`** — The name of the field taken from each entry in the array. The element name is the same for every entry; the utility collects its value from each one.

**`-CaptureTag`** — The pipe-separated path to the array within the response, in the same form used by SMAParseResponseFile.

**List file** — The output of this utility. A file of captured values, one per line, comma-terminated except for the final line.
