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

### -CSVContainerTag

Names the container object holding the array to convert to a CSV file. Required to produce a CSV.

### -CSVOutputFilename

Defines the path and file name of the CSV file to create.

### -CSVIncludeHeaders

When specified, writes a header row of column names as the first line of the CSV file. Include the parameter on its own; it takes no value.

### -FileDataFilename

If this value is specified, the indicated file is read and its contents are inserted as the value of a `FileData` entry in the request file.

### -Request

Defines the request specification appended to the `BaseURL` to select the desired operation.

### -RequestFile

Defines the file containing the formatted parameters required for the request.

### -ResponseOutputFilename

Saves the raw response to a file for downstream processing. Use this when tag values need to be extracted with SMAParseResponseFile.

:::caution A saved response file can contain borrower data
The response carries whatever the operation returned, which for these operations includes borrower names, loan identifiers and payment detail. The saved file is an ordinary part of the workflow, so it is not something to switch off — but store it where the job's own output would be protected, remove it once the downstream step has consumed it, and treat it as sensitive if you attach it to a support case.
:::

### -SystemDate

Specifies the processing date when the current date should not be used. The format is `YYYY-MM-DD`.

:::tip Use a property so every job in the schedule agrees
Refer to [Reference information](./reference.md) for the recommended way to hold one timestamp across an entire processing day and pass it as `-SystemDate=[[FICS_Timestamp]]`.
:::

:::note
If this parameter is not specified, the current datetime stamp is inserted into the request file.
:::

### -VerboseLogging

When specified, writes the request URI and the full request body to the log file.

### -DumpResponseContent

When specified, writes the raw response body to the log file under a `Raw Response Content:` heading.

This parameter also changes how the response is collected. With `-DumpResponseContent`, the connector waits for the complete response before continuing. Without it, the response is collected asynchronously and only the number of bytes returned is logged. Use it together with `-VerboseLogging` when you need to see both sides of an exchange.

:::caution This output can contain borrower data and a credential
The request carries the FICS authorization token, and the response carries whatever the operation returned, which for these operations includes borrower names, loan identifiers and payment detail. Turn verbose logging off once you have finished troubleshooting, and treat the log file and any saved response file as sensitive when storing them or attaching them to a support case.
:::

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
| `TreatNoDataAsError` | `true` | Controls whether a response that contains no data is treated as an error condition. When `true`, a no-data response exits `1` and `ExitValueForNoData` is ignored. |
| `ExitValueForNoData` | `0` | Defines the exit code returned when a no-data response is received. **Consulted only when `TreatNoDataAsError` is `false`.** |

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

## Exit codes

| Code | Meaning |
|---|---|
| `0` | The request completed and the response contained data. |
| `1` | The request failed, **or** the response contained no data and `TreatNoDataAsError` is `true`. |
| `ExitValueForNoData` | The response contained no data and `TreatNoDataAsError` is `false`. Defaults to `0`. |

Set the **Failure Criteria** for the OpCon job to **NE** (Not Equal) to `0` to fail the job on an error.

:::note An empty result fails the job at the shipped defaults
The sample configuration file sets `TreatNoDataAsError=true` and `ExitValueForNoData=0`. In that combination a response with no data exits `1`, and the `ExitValueForNoData` value is never used. To let an empty result pass, set `TreatNoDataAsError=false` and give `ExitValueForNoData` the code you want.
:::

## FICS API call quota

FICS returns the number of web service calls remaining on the account in an `ApiCallsLeft` response header. SMAFICSConnector records the value in its log on every run:

```
ApiCallsLeft = 4821
```

:::caution The job is failed deliberately when the quota reaches zero
When the remaining count reaches `0`, SMAFICSConnector stops and writes the following to the log before failing the job:

```
Job failed because ApiCallsLeft went to 0.
```

This is not an OpCon or connector problem and it is not resolved by rerunning. The limit is set on the FICS side, so raising it is a conversation with FICS. The logged value on earlier runs is the warning you have: watch it if your schedule makes many calls in a day.
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

**What is the difference between `-VerboseLogging` and `-DumpResponseContent`?**

`-VerboseLogging` writes the request URI and the request body. `-DumpResponseContent` writes the raw response body, and also makes the connector wait for the complete response instead of collecting it asynchronously. Use both together to see a full exchange. Both write data you should treat as sensitive.

**Why did my job fail with `Job failed because ApiCallsLeft went to 0`?**

The FICS account has used its allocation of web service calls. SMAFICSConnector reports the remaining count in its log on every run and fails the job when it reaches zero. Rerunning does not help; the limit is raised on the FICS side.

## Glossary

**Request file** — A text file containing the formatted JSON or XML parameters for a FICS web service operation. Referenced by `-RequestFile` on the command line.

**BaseURL** — The base URL of the FICS web service, defined in `SMAFICSConnector.ini`. The `-Request` parameter value is appended to this URL to form the complete endpoint address.

**Authorization token** — A credential used to authenticate with the FICS web service. Passing the token via `-AuthorizationToken` avoids generating a new token on each run.

**SMA_INJECT_FILE** — A directive placed in a request file that causes SMAFICSConnector to read a second file and insert its contents inline at that position in the request.

**DocumentCollection** — A named array in a FICS response that contains one or more documents. Use `-DocumentCollectionName` to specify a non-default collection name.

**`ApiCallsLeft`** — A FICS response header giving the number of web service calls remaining on the account. SMAFICSConnector logs it on every run and fails the job when it reaches zero.
