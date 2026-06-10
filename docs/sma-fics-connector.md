---
title: SMAFICSConnector
description: "Command-line reference for SMAFICSConnector, the utility that submits FICS request files to FICS Mortgage Servicer and Mortgage Accountant web services."
sidebar_label: SMAFICSConnector
tags:
  - Reference
  - Automation Engineer
  - Getting Started
---

# SMAFICSConnector

## What is it?

SMAFICSConnector is a command-line utility that connects to the FICS API and submits a request file to the appropriate web service interface (Mortgage Servicer, Mortgage Accountant, etc.).

- Use SMAFICSConnector as the executable in an OpCon job to run FICS operations on a schedule
- Use the `-VerboseLogging` parameter when troubleshooting to capture the full request and response in the log file

:::tip Example

```
SMAFICSConnector.exe -VerboseLogging -Request=GetFannieMaeGuarantyFeeSummary -RequestFile=.\BasicMessage.txt
```

:::

## Command line options

### -AuthorizationToken

Specifies the token to use for authentication. This option reduces the number of FICS requests because a new token is not generated on each run.

### -ConfigFile

Defines the configuration file to use if `SMAFICSConnector.ini` is not the desired configuration file.

### -DocumentCollectionName

If a document collection is to be processed, the default collection name is `DocumentCollection`. This parameter allows you to change the collection name to search.

### -Document1 ... -Document99

Sometimes documents are returned in collections (or arrays). If so, the appropriate document can be saved by specifying the desired path and file name.

:::tip Example

To save a specific document from a collection, specify:

```
-Document4=<path and name of file>
```

:::

If a document specifier is not included on the command line, the document is not saved to disk. No error is generated for `-Document` parameters that have no corresponding document in the response.

### -DocumentBase64TagFilename

If a `DocumentBase64` tag is found in the response, the contents are stored in this file. This argument overrides `-OutputFilename` for `DocumentBase64` tags. If the keyword `SUPPRESS` is used instead of a file path, no output file is created for this tag.

### -FileTagFilename

If a `File` tag is found in the response, the contents are stored in this file. This argument overrides `-OutputFilename` for `File` tags. If the keyword `SUPPRESS` is used instead of a file path, no output file is created for this tag.

### -OutputFilename

If a report is returned, this is the path and file name under which to store the report. If either a `File` tag or a `DocumentBase64` tag is found, the contents are stored in this file. This can be overridden by `-FileTagFilename` or `-DocumentBase64TagFilename`. It is possible to have both a `File` tag and a `DocumentBase64` tag in the same response.

:::note
`-OutputFilename` has been deprecated and will be removed in a future version of SMAFICSConnector. Use `-FileTagFilename` or `-DocumentBase64TagFilename` instead, or use SMAParseResponseFile.
:::

### -FileDataFilename

If this value is specified, the indicated file is read and its contents are inserted as the value of a `FileData` entry in the request file.

### -Request

Defines the request specification appended to the `BaseURL` to select the desired operation.

### -RequestFile

Defines the file containing the formatted parameters required for the request.

### -ResponceOutputFilename

Saves the raw response XML to a file for downstream processing. Use this when tag values need to be extracted with SMAParseResponseFile.

### -SystemData

Specifies the processing date when the current date should not be used. The format is `YYYY-MM-DD`.

:::note
If this parameter is not specified, the current datetime stamp is inserted into the request file.
:::

### -VerboseLogging

When specified, dumps the request and raw response to the log file.

---

:::note About request files

Besides containing data, request files can include OpCon global property tokens and Schedule Instance property tokens using the `[[Property Name]]` syntax. Only User Defined Properties are supported — System Properties and Managed System Properties are not.

There is a special directive called `SMA_INJECT_FILE`. This directive causes the contents of a specified file to be included inline at that point in the request. Specify it on its own line:

```
SMA_INJECT_FILE=.\injectedLines.txt
```

This instructs SMAFICSConnector to read `.\injectedLines.txt` and insert its lines at that point in the request packet.

:::

:::tip Example: CSV files

The `-CSVContainerTag` parameter must indicate the container object of the array to convert to a CSV file. In the following example response, the container object is `Data`:

```json
{
    "GlobalTotal": null,
    "Data": {
        "DataSource": [
            {
                "LoanId": 833.0,
                "LoanName": "SMITH, JANE L",
                "DueDate": "2015-05-01T00:00:00",
                "StopCode": null,
                "PhCheckFee": 0.0
            },
            {
                "LoanId": 4038.0,
                "LoanName": "SMITH, Janet R",
                "DueDate": "2015-05-01T00:00:00",
                "StopCode": null,
                "PhCheckFee": 0.0
            },
            {
                "LoanId": 4218.0,
                "LoanName": "SMITH, JANE L",
                "DueDate": "2015-05-01T00:00:00",
                "StopCode": null,
                "PhCheckFee": 0.0
            }
        ]
    },
    "APISuccessful": true
}
```

On the command line, specify:

```
-CSVContainerTag="Data" -CSVOutputFilename="C:\myfile.csv" -CSVIncludeHeaders
```

:::

## Configuration settings

SMAFICSConnector reads settings from `SMAFICSConnector.ini` at startup. The following is an example configuration file:

```
#####################################################################
#
#    This configuration file is used by
#    SMAFICSConnector.
#
#####################################################################
[General]

[Execution Control Parameters]
RequestTimeoutInMilliseconds=120000
TreatNoDataAsError=true
ExitValueForNoData=0

[Resource Contention Parameters]
RCRetryFrequencyInMilliseconds=60000
RCMaximumAttempts=10

[Web Service Connection Parameters]
TokenURL=http://MortgageServicer.FICS/MortgageServicerService.svc/REST/
BaseURL=http://MortgageServicer.FICS/MortgageServicerService.svc/REST/
LoginUser=opcon
LoginPassword=.\FICSPassword.dat
LoginConnectionName=FICS MSS
LoginTimeoutInMilliseconds=120000

[OpCon Database Parameters]
OpConDBUser=opconui
OpConDBPassword=.\OpConPassword.dat
OpConDBServer=SQLSERVER\SQLEXPRESS
OpConDBName=OPCONXPS
```

### Execution Control Parameters

| Setting | Default | What it does |
|---|---|---|
| `RequestTimeoutInMilliseconds` | `120000` | Defines the maximum number of milliseconds to wait for a web service request to complete before timing out. |
| `TreatNoDataAsError` | `true` | Controls whether a response that contains no data is treated as an error condition. |
| `ExitValueForNoData` | `0` | Defines the exit code returned when a no-data response is received. |

### Resource Contention Parameters

| Setting | Default | What it does |
|---|---|---|
| `RCRetryFrequencyInMilliseconds` | `60000` | Defines how many milliseconds to wait between retry attempts when a resource contention condition is detected. |
| `RCMaximumAttempts` | `10` | Defines the maximum number of retry attempts before SMAFICSConnector stops retrying and exits with an error. |

### Web Service Connection Parameters

| Setting | Default | What it does |
|---|---|---|
| `TokenURL` | *(none)* | Defines the base URL for the FICS web service used during authentication. |
| `BaseURL` | *(none)* | Defines the base URL for web service method calls. The `-Request` command-line parameter value is appended to this URL to form the complete endpoint. The correct value depends on the FICS module in use: Mortgage Servicer regular methods, Mortgage Servicer Specials, and Mortgage Accountant each use a different base URI. FICS can supply the correct URL for each module. |
| `LoginUser` | *(none)* | Defines the FICS user account used to authenticate with the web service. |
| `LoginPassword` | *(none)* | Defines the path to the encrypted password file for `LoginUser`. Create this file using SMACreatePasswordFile. |
| `LoginConnectionName` | *(none)* | Defines the FICS connection name (database) to connect to. Must match the `LoginConnectionName` in `SMAFICSTemplateEditor.ini` if both tools are in use. |
| `LoginTimeoutInMilliseconds` | `120000` | Defines the maximum number of milliseconds to wait for the login operation to complete before timing out. |

### OpCon Database Parameters

| Setting | Default | What it does |
|---|---|---|
| `OpConDBUser` | *(none)* | Defines the SQL user account used to connect to the OpCon database. |
| `OpConDBPassword` | *(none)* | Defines the path to the encrypted password file for `OpConDBUser`. |
| `OpConDBServer` | *(none)* | Defines the server name and instance of the SQL Server that hosts the OpCon database. |
| `OpConDBName` | *(none)* | Defines the name of the OpCon database. |

:::note
If `OpConDBUser` and `OpConDBPassword` are left blank, Windows Authentication to the OpCon database is attempted. The OpCon job must specify a domain user in the **User Id** field on the job details tab.
:::

**Related topics:**

- [FICS Connector overview](./overview.md)
- [SMAFICSTemplateEditor](./sma-fics-template-editor.md)
- [SMAParseResponseFile](./sma-parse-response-file.md)
- [Reference information](./reference.md)

## FAQs

**How do I use a different configuration file?**

Use the `-ConfigFile` parameter to specify the path to an alternative configuration file. For example: `-ConfigFile=.\MyConfig.ini`.

**What is the difference between `-FileTagFilename` and `-DocumentBase64TagFilename`?**

Both override `-OutputFilename` for their respective tag types. Use `-FileTagFilename` when the response contains a `File` tag, and `-DocumentBase64TagFilename` when it contains a `DocumentBase64` tag. A response can contain both tag types simultaneously.

**Can I pass OpCon schedule dates or properties as part of the request?**

Yes. Use the `[[Property Name]]` token syntax in your request file. OpCon substitutes the property value before SMAFICSConnector sends the request. Only User Defined Properties and Schedule Instance properties are supported.

**What does `-VerboseLogging` write to the log?**

With verbose logging enabled, SMAFICSConnector writes the full request body and the raw response XML to the connector log file. Use this for troubleshooting failed or unexpected responses.

## Glossary

**Request file** — A text file containing the formatted JSON or XML parameters for a FICS web service operation. Referenced by `-RequestFile` on the command line.

**BaseURL** — The base URL of the FICS web service, defined in `SMAFICSConnector.ini`. The `-Request` parameter value is appended to this URL to form the complete endpoint address.

**Authorization token** — A credential used to authenticate with the FICS web service. Passing the token via `-AuthorizationToken` avoids generating a new token on each run.

**SMA_INJECT_FILE** — A directive placed in a request file that causes SMAFICSConnector to read a second file and insert its contents inline at that position in the request.

**DocumentCollection** — A named array in a FICS response that contains one or more documents. Use `-DocumentCollectionName` to specify a non-default collection name.
