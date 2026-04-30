---
title: SMAParseResponseFile
description: "Command-line reference for SMAParseResponseFile, the utility that extracts tag values from FICS web service response files and saves them to OpCon properties or files."
sidebar_label: SMAParseResponseFile
tags:
  - Reference
  - Automation Engineer
  - Getting Started
---

# SMAParseResponseFile

## What is it?

SMAParseResponseFile parses a saved FICS web service response file to extract a tag value or structure, and saves the result to either an OpCon global property or a file. The saved file can then be used with the `SMA_INJECT_FILE` directive to complete a request file for a downstream web service method.

- Use this utility when a downstream FICS job requires a value that was returned by an earlier FICS job in the same schedule
- Use this utility to extract document content (such as a Base64-encoded report) from a FICS response for downstream processing

:::tip Example

```
SMAParseResponseFile.exe -ResponseFile=.\MyResponse.txt -CaptureTag="DataSource" -PropertyName="DataSourceArray"
```

or

```
SMAParseResponseFile.exe -ResponseFile=.\MyResponse.txt -CaptureTag="DataSource" -TagValueFilename=".\MyParsedResponse.txt"
```

:::

## Command line options

### -CaptureTag

Defines the tag whose value needs to be captured. If the tag is nested, specify the full path to it using pipe (`|`) separators.

Consider a response file containing:

```json
{
    "Content": {
        "ResultSet": {
            "table1": [
                {
                    "Bank": "101",
                    "Investor": "033",
                    "Group": "001",
                    "Investor Name": "Freddie Mac",
                    "Reporting Method": "Freddie Mac"
                }
            ]
        }
    },
    "ApiCallSuccessful": true,
    "ApiCallsLeft": "1997"
}
```

To capture the value of **Bank**, specify:

```
-CaptureTag="Content|ResultSet|table1|Bank"
```

Note the brackets following `table1`. This indicates that an array of values is returned. SMAParseResponseFile returns the value of the last member of the array.

If the desired report is in a Document Collection, you can specify the Document Collection along with the `Name` property of the report to retrieve.

:::tip Example

Given a response file like the following:

```json
{
    "GlobalTotal": null,
    "DocumentCollection": [
        {
            "DocumentBase64": "JVBERi0xLjMNCiXi48/TDQoxIDB...",
            "Name": "Document 1"
        },
        {
            "DocumentBase64": "JVBERi0xLjMNCiXi48/TDQoxIDA...",
            "Name": "Document 2"
        }
    ]
}
```

To retrieve **Document 2**, specify:

```
-CaptureTag="DocumentCollection[Document 2]"
```

If `-OutputFormat=CSV`, `-CaptureTag` names the container object of the array to save as a CSV file.

:::

### -ConfigFile

Specifies the configuration file to use if `SMAParseResponseFile.ini` is not the desired configuration file.

### -CSVIncludeHeaders

When specified, property names are included as the first line of the CSV file.

### -MissingFileExitValue

Defines an exit code to use when the specified response file cannot be found. By default, SMAParseResponseFile exits with value `1` on error. Use this parameter to specify a different exit value for the missing-file condition.

### -OutputFormat

Optional. By default, the tag value is saved as a JSON construct suitable for use with `SMA_INJECT_FILE`.

:::tip Example

Default JSON output:

```json
[
    {
        "Bank": "87",
        "Investor": "087",
        "Group": "001",
        "Investor Name": "Freddie Mac",
        "Reporting Method": "Freddie Mac"
    }
]
```

:::

For response files that contain non-standard layouts that SMAFICSConnector cannot parse, use `-OutputFormat=DATA`:

:::tip Example

For a response containing:

```
{
    "LateNoticeSummaryReport": {
        "Document": {
            "DocumentBase64": "JVBERi0xLjIDAgb2JqDQo..."
```

Setting `-OutputFormat=DATA` and `-CaptureTag="LateNoticeSummaryReport|Document|DocumentBase64"` saves the tag contents directly to `-TagValueFilename`.

:::

:::note
If `-OutputFormat=DATA` is specified, `-PropertyName` and `-TranslationFilename` cannot be used.

If `-OutputFormat=CSV`, a CSV file is created from the members of the array in the container object specified by `-CaptureTag`.
:::

### -PropertyName

Defines the name of the OpCon global property in which to save the captured tag value.

### -ResponseFile

Defines the file containing the FICS web service response. This file is typically created by SMAFICSConnector using the `-ResponceOutputFilename` parameter.

### -TagValueFilename

Defines the name of the file in which to save the captured tag value.

### -TranslationFilename

Optional. Allows you to remap tag names in the output.

:::tip Example

If one FICS query returns the tag name `"Investor Name"` but a downstream operation requires `"InvestorName"` without spaces, create a translation file like the following:

```
[Tag Translations]
Translation1="Investor Name":|"InvestorName":|
Translation2="Reporting Method":|"InvestorReportingCode"|
```

The current tag name is followed by the desired tag name. Both must be terminated with the vertical pipe symbol (`|`). Up to 100 translations are supported.

:::

## Configuration settings

SMAParseResponseFile uses a configuration file to connect to the OpCon database when saving values to global properties. The following is an example configuration file:

```
##############################################
#
#    This configuration file is used by
#    SMAParseResponseFile
#
##############################################
[General]

[OpCon Database Parameters]
OpConDBUser=
OpConDBPassword=
OpConDBServer=
OpConDBName=
```

| Setting | What it does |
|---|---|
| `OpConDBUser` | Defines the SQL user account used to connect to the OpCon database. |
| `OpConDBPassword` | Defines the password (or path to the encrypted password file) for the `OpConDBUser`. |
| `OpConDBServer` | Defines the server name and instance of the SQL Server that hosts the OpCon database. |
| `OpConDBName` | Defines the name of the OpCon database. |

:::note
If `OpConDBUser` and `OpConDBPassword` are left blank, Windows Authentication to the OpCon database is attempted. The OpCon job must specify a domain user in the **User Id** field on the job details tab.
:::

**Related topics:**

- [FICS Connector overview](./overview.md)
- [SMAFICSConnector](./sma-fics-connector.md)
- [Reference information](./reference.md)

## FAQs

**What format does the response file use?**

The response file is a JSON file saved by SMAFICSConnector using the `-ResponceOutputFilename` parameter. SMAParseResponseFile reads this file and extracts the specified tag value.

**Can I save a captured value to both a property and a file?**

No. You must use either `-PropertyName` (to save to an OpCon global property) or `-TagValueFilename` (to save to a file), not both in the same run.

**What happens if the response file is not found?**

By default, SMAParseResponseFile exits with value `1`. Use `-MissingFileExitValue` to specify a different exit code for this condition, which allows downstream OpCon jobs to respond differently to a missing file versus other errors.

**How do I handle a tag name with spaces?**

Use `-TranslationFilename` to remap tag names that contain spaces to names without spaces for use in downstream FICS operations.

## Glossary

**CaptureTag** — The tag name (or pipe-separated path to a nested tag) whose value SMAParseResponseFile extracts from the response file.

**Response file** — A JSON file containing the raw output of a FICS web service call. Created by SMAFICSConnector when `-ResponceOutputFilename` is specified.

**Translation file** — An INI-style file that maps source tag names to replacement tag names. Used when a downstream FICS operation requires different field names than what the source response provides.

**SMA_INJECT_FILE** — A directive in a FICS request file that instructs SMAFICSConnector to read and insert the contents of a second file at that position in the request.
