---
sidebar_label: 'SMAParseResponceFile'
---

# SMA Parse Responce File

## Overview

There are occasions when tag values need to be pulled and saved for downstream processing from one of the responses to a web method. The SMAParseResponseFile application allows users to parse a saved response file to grab a value (or a structure) and save it in either an OpCon global property or a file. (This file can then be used with the SMA_INJECT_FILE directive to complete a request file for a web method.)

:::tip Example

SMAParseReponseFile.exe -ReponseFile=.\MyResponce.txt -CaptureTag="DataSource" -PropertyName="DataSourceArray"

or

SMAParseReponseFile.exe -ReponseFile=.\MyResponce.txt -CaptureTag="DataSource" -TagValueFilename=".\MyParsedReponse.txt"

:::

## Command Line Options

### -CaptureTag
This defines the tag whose value needs to be captured. If it occures in a nested fashion, specify the path to is. Consider a response file containing:

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

If the value of **"Bank"** was desired, the user should specify:

```-CaptureTag="Content|ResultSet|table1|Bank"```

Also, note the brackets following **"table1"**. This indicates that there are an array of values returned. SMAParseResponseFile returns the value of the last member of the array.

If the desired report to retrieve, i.e., OutputFormat=DATA, is in a Docu- ment Collection, you can specify the Document Collection along with the Name property of the report that you wish to retrieve.

:::tip Example

The response file appears like the following:
```
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
        },
... 
}
```

The report called **"Document 2"** could be retreived by the following specifications on the command line:
```-CatupreTag="DocumentCollection[Document 2]"```

If -OutputFormat=CSV, this is the name of the container object of the array that is to be saved in a .csv file.

:::

### -ConfigFile

If the desired configuration file is not SMAParseResponseFile.ini, it can be specified here.

### -CSVInludeHeaders

Specify this if the property names should be included as the first line of the .csv file.

### -MissingFileExitValue

It may be desirable to exit with a value other than the general error exit value (1). If the specified file cannot be found, SMAParseFile will exit with the value specified by this parameter

### -OutputFormat

This is an optional parameter. By default, the tag value will be saved as a JSON construct to be used in an SMA_INJECT_FILE specified. 

:::tip Example
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

However, there may be response files that contain "non-standard" layouts that SMAFICSConnector cannot parse. 

:::tip Example

Consider this snippet:

```
{
    "LateNoticeSummaryReport": {
        "Document": {
            "DocumentBase64": "JVBERi0xLjIDAgb2JqDQo..."
```

If ```–OutputFormat``` is set to ```DATA``` and ```–CaptureTag``` is set to ```"LateNoticeSummaryReport|Document|DocumentBase64"```, the tag contents will be saved in ```–TagValueFilename```.
:::

:::info Note
If ```–OutputFormat=DATA``` is specified, then use of ```–PropertyName``` and ```–TranslationFile``` are not allowed.

If ```–OutputFormat``` is set to ```CSV```, then a CSV file will be created from the members of the array that are in the container object specified by ```–CaptureTag```.

:::

### -PropertyName

Defines the command line argument to designate the name of the file if the value is to be saved in a global property.

### -ResponceFile

Defines the file containing the response from the web services. It is expected that this file will be created from SMAFICSConnector (by adding the command line parameter –ResponseOutputFilename).

### -TagValueFileName

Defines the command line argument to designate the name of the file if the value is to be saved in a file.

### -TranslationFileName

This is an optional argument. It allows the user to re-map tag names. 

:::tip Example

If  one FICS query returns the tag name as **"Investor Name"**. However, to use that value in another FICS operation, it must be **"InvestorName"** with no spaces. 

The translation file could look like:

```
[Tag Translations]
Translation1="Investor Name":|"InvestorName":| 
Translator2="Reporting Method":|"InvestorReportingCode"|
```

The current tag name is followed by the desired tag name. Both must be ter- minated with the vertical pipe symbol. Up to 100 translations are supported.

:::

## Configuration Settings

SMAParseResponseFile does have a configuration file so that it knows how to update the propert once a value is found in the Repoonce File. Below is an example of the Configuration File.

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

### OpConDBUser

Defines the SQL user account to use to connect to the OpCon Database.

:::info Note

If OpConDBUser and OpConDBPassword are left blank, Windows Authentication ot the OpCon database will be attempted. This means the job must specify a domain user in the "User Id" field on the job details tab.
:::

### OpconDBPassword

Defines the password (or the path to the encrypted password file) for the OpConDBUser.

:::info Note

If OpConDBUser and OpConDBPassword are left blank, Windows Authentication ot the OpCon database will be attempted. This means the job must specify a domain user in the "User Id" field on the job details tab.
:::

### OpConDBServer

Defines the server name (and instance) of the SQL Server that hosts the OpCon database.

### OpConDBName

Defines the name of the OpCon database.



