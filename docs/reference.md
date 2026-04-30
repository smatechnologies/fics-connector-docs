---
title: Reference information
description: "Strategic guidance and technical reference for building and managing FICS request files, including datetime handling and OpCon property integration."
sidebar_label: Reference information
tags:
  - Reference
  - Automation Engineer
  - Getting Started
---

# Reference information

## What is it?

This page provides strategic guidance for building FICS request files and integrating FICS jobs with OpCon properties.

- Use this page when setting up a new FICS operation to understand the recommended file-based workflow
- Use this page when you need guidance on timestamp coordination across multiple FICS jobs in the same schedule

## Strategy

The request for a particular operation can be quite lengthy because there are numerous parameters that can or must be set. For example, consider one of the simpler requests for the "Get Bill and Receipt Statement Report" operation:

```json
{
   "Message": {
      "JobId": "sample string",
      "CommentPage": "sample string",
      "CommentLine": "sample string",
      "IncludeDisbursement": false,
      "IncludeInterest": false,
      "IncludePayInFull": false,
      "IncludeLateCharges": false,
      "IncludeReturnCheckCharges": false,
      "IncludeMiscellaneousFees": false,
      "IncludeBankFees": false,
      "IncludeAutomatedClearingHouse": false,
      "IncludePayrollDeduction": false,
      "SuppressPrintingCompanyName": false,
      "SendEmail": false,
      "SuppressPrintingCommentPage": false,
      "SuppressPrintingDelinquencyNotice": false,
      "FilePath": "sample string",
      "BeginningTransactionDate": "2015-03-03T15:25:46",
      "EndingTransactionDate": "2015-03-03T15:25:46",
      "IncludeLoansDueOn": "2015-03-03T15:25:46",
      "SystemDate": "2015-03-03T15:25:46",
      "Token": "sample string"
   }
}
```

Additionally, the number and composition of a request can change as new releases of Mortgage Servicer are released. To provide a flexible mechanism that allows you to manage parameter changes without requiring new connector versions, the following approach is recommended:

1. Open the API documentation for FICS in a web browser. This URL is different for each customer and can be supplied by FICS.
2. Locate the operation to be performed (for example, Billing > Bill and Receipt). Expand the **Example Request** link.
3. Copy the request text and paste it into a text file.
4. Edit the text file directly or use SMAFICSTemplateEditor. If you edit the file using a text editor such as Notepad, you must maintain the proper format and structure. SMAFICSTemplateEditor maintains the format automatically.
5. Determine the values to use by manually walking through the process in Mortgage Servicer and recording the values and field labels. Enter these values into the text file. Replace dynamic values with OpCon properties using the `[[Property Name]]` format. For example, dates would most likely be calculated by another process and stored as a property, which any FICS operation requiring that date can then reference.
6. Set up an OpCon job to run SMAFICSConnector. The command line must specify the name of the request file and the operation to be performed.

## Notes on datetime handling

Some FICS processes require the same timestamp across multiple jobs in the same schedule. For example, processing a daily report bundle requires a consistent timestamp across the entire update cycle. Instead of capturing the timestamp from a response, use the following strategy:

1. Create a global property called `FICS_Timestamp`. The initial value is not important.
2. Create a global property called `$NOWYYYY-MM-DDhh:nn`. Set its value to `YYYY-MM-DDhh:nn`.
3. Create a Null job at the beginning of each processing day with the following event:
   - `$Property:Set,FICS_TimeStamp,$NOWYYYY-MM-DDhh:nn`

Use `[[FICS_Timestamp]]` in any request file that requires timestamp coordination.

:::tip

If you set up `FICS_Timestamp` as described above, you can specify `-SystemDate=[[FICS_Timestamp]]` on every SMAFICSConnector job. This makes reruns significantly easier if they are ever required.

:::

**Related topics:**

- [FICS Connector overview](./overview.md)
- [SMAFICSConnector](./sma-fics-connector.md)
- [SMAFICSTemplateEditor](./sma-fics-template-editor.md)
- [SMAParseResponseFile](./sma-parse-response-file.md)

## FAQs

**Why is a file-based approach recommended instead of hardcoding values?**

FICS request parameters can change between Mortgage Servicer releases. Storing request definitions in text files allows you to update parameters without modifying OpCon job definitions or requiring a new connector version.

**Can I use OpCon properties in request files?**

Yes. Use the `[[Property Name]]` token syntax in request files. OpCon substitutes the property value before SMAFICSConnector sends the request. Only User Defined Properties and Schedule Instance properties are supported.

**What is the recommended approach for timestamp coordination across multiple FICS jobs?**

Create a `FICS_Timestamp` global property and set it at the start of each processing day using a Null job and a `$Property:Set` event. Reference `[[FICS_Timestamp]]` in all request files that require a consistent timestamp, and pass it as `-SystemDate=[[FICS_Timestamp]]` on every SMAFICSConnector command line.

## Glossary

**Request file** — A text file containing the formatted JSON parameters for a FICS web service operation. Referenced by the `-RequestFile` parameter on the SMAFICSConnector command line.

**`[[Property Name]]`** — The OpCon token syntax used to reference a global property or Schedule Instance property value in a request file. Substituted by OpCon before the request is sent.

**`FICS_Timestamp`** — A recommended global property name used to store a consistent datetime value for use across multiple FICS jobs in the same schedule.

**`-SystemDate`** — A SMAFICSConnector command-line parameter that specifies the processing date. Using `[[FICS_Timestamp]]` as the value ensures consistent datetime handling and simplifies reruns.

**Null job** — An OpCon job type that performs no work itself but can fire events, such as setting property values, at a defined point in a schedule.
