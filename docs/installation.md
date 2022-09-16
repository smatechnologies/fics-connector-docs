---
sidebar_label: 'Installation'
---

# Installation

## Overview

Running FICS Mortgage Servicer jobs or FICS Mortgage Accountant jobs can be accomplished by using the applications included in this connector. The exposed interface point (from FICS) is instantiated in the form of web services. SMA Technologies provides the tools to allow users to craft request files then submit them to the appropriate web service interface.

## Requirements

Any supported version of Windows with .NET Framework 4.5 must be installed.

## Install Process

Download the SMAFICSConnector.zip which contains the OpCon compoents to successfully automate FICS jobs via OpCon.

Once downloaded, unzip the connector in the same path where OpCon is installed. You may need to create an FICS directory in ProgramData if you installed OpCon on the C Drive.

:::tip Example

If OpCon is installed on the C-drive, your FICS installaion would be broken out in two locations:

* Executables and DLLs would be in ```C:\Program Files\OpConxps\FICS```
* INI Files would be in ```C:\ProgramData\FICS```

If OpCon is installed on a non-system drive, your FICS installation would be contained in one path: 

* Executables, DLLs, and INI files would all be in ```D:\OpConxps\FICS```
:::