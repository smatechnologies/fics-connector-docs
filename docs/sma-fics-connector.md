---
sidebar_label: 'SMAFICSConnector'
---

# SMA FICS Connector

## Overview

This command-line utility connects to the FICS API in order to sumit the request file to the appropriate web service interface (Mortgage Servier, Mortgage Accountant, ect.).

:::tip Example

SMAFICSConnector.exe -VerboseLogging -Request=GetFannieMaeGuarantyFeeSummary -RequestFile=.\BasicMessage.txt

:::


## Command Line

### -AuthorizationToken

Dictates that the token specified should be used for authentication. The option will reduce the number of FICS requests as a new token will not be generated each time.

### -ConfigFile

Defines the configuration file to use if SMAFICSConnector.ini is not the desired configuration file.

### -DocumentCollectionName

If a document collection is to be processed, the default name for the collection is "DocumentCollection." This command line parameter allows the user to change the name of the collection to be searched.

### -Document1 ... -Document99

Sometimes, documents are returned in "collections" (or "arrays"). If so, the appropriate document can be saved by specifying the desired path and name. 

:::tip Example

If a collection is desired, you can specify: ```-Document4=<path and name of file>```

:::

If a document specifier is not included on the command line, the document will not be saved as a file on the disk. It does **NOT** generate an error for ```-Document``` commands that are included on the command line that have no corresponding documents returned.

### -DocumentBase64TagFilename

If a ```DocumentBase64``` tag is found, the contents are stored in this file. This argument overrides the ```–OutputFilename``` argument (for ```DocumentBase64``` tags). If the keyword **SUPPRESS** is used instead of a file path and name, no output file will be created for this tag.

### -FileTagFilename

If a ```File``` tag is found, the contents are stored in this file. This argument overrides the ```–OutputFilename``` argument (for "File" tags). If the keyword **SUPPRESS** is used instead of a file path and name, no output file will be created for this tag.

### -OutputFilename

If a report is returned, this is the path (and name) under which to store the report. If either a "File" tag or a "DocumentBase64" tag is found, the contents are stored in this file. This can be overridden by utilizing the ```-FileTageFilename``` argument or the ```-DocumentBase64TagFilename``` argument. If the response is the same, it is possible to have BOTH a "File" tag and a "DocumentBase64" tag.

```-OutputFilename``` has been deprecated and will be removed in a future version of SMAFICSConnector. Use either ```-FileTagname``` or ```-DocumentBase64TagFilename``` (or preferably, use SMAParseResponseFile).

### -FileDataFilename

If this value is specified, the file indicated is read and the contents are inserted as the value of a FileData entry in the response file.

### -Request

Defines the request specification that will be appended to the BaseURL to select the desired operation.

### -RequestFile

Defines the file containing the formatted parameters required for the request.


### -ResponceOutputFilename

This parameter allows the user to save the raw response XML in a file for downstream processing. For example, certain tag values may need to be extracted with SMAParseResponseFile.

### -SystemData

If the current date is NOT to be used for the processing date, the user can specify the processing date by setting ```–SystemDate```. The format is YYYY-MM-DD.


:::info Note

If this parameter is NOT specified, the current datetime stamp will be inserted into the request file.

:::

### -VerboseLogging

If this value is specified, the request and raw response is dumped to the log file.

:::info Note about Request Files

Besides containing data, the request file can have tag values that are global properties and Schedule Instance properties. The only supported global properties are User Defined Properties, not System Properties or Managed System Properties.

There is a special designation called SMA_INJECT_FILE. This directive will cause the contents of the specified file to be included inline. It should be specified on a line by itself as in: ```SMA_INJECT_FILE=.\injectedLines.txt```

This would instruct SMAFICSConnector to read ```.\injectedLines.txt``` and insert the lines at this point in the request packet.

:::

:::tip Example about CSV Files

The ```-CSVContainerTag``` command line argument must indicate the container object of the array to be converted to the .csv file. A seen in the following simple file, the container object is ```Data```. So, to create a .csv file called ```C:\Example.csv``` with headers.

```JSON
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
    "APISuccessful" : true
}
```

On the command line, you would specify: ```-CSVContainerTag="Data" -CSVOutputFilename="C:\myfile.csv" -CSVIncludeHeaders```

:::
