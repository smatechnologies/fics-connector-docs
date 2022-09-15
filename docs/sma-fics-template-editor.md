---
sidebar_label: 'SMAFICSTemplateEditor'
---

# SMA FICS Template Editor

## Overview

SMAFICSTemplateEditor is a Windows-based simple editor that allows users to modify the template file copied from the documentation website. The template editor maintains the data types of each of the fields and the format of the request. It is important to realize that this tool is optional.

:::info Note

If a required data structure cannot be built by the available functions, the request file can be edited in Notepad (or another suitable editor).
:::

## Walkthrough

When a template file is first opened, it appears similar to the following:

![img alt](/img/smaficstemplate1.png)

At this point, the user can read an existing request file either by clicking the **Read File** button or by clicking the **Fetch Method Definitions** button to load the available definitions (from the web service) and then select one of the functions by clicking the **Select Method Template** button.

**IF** the user clicks on the **Fetch Method Definitions** button, a wait cursor appears and then all of the buttons become enabled. If the user then clicks the **Select Method Template** button, a screen similar to the one below is displayed.

![img alt](/img/smaficstemplate2.png)

This represents a list sorted by Folder, then Sub-Folder, and then Method Name. Since some of the Program Names are duplicated, e.g., ```Reprint Investors (s)```, the actual Method Name is shown in brackets to help identify the desired functionality.

Click one of the Program Name lines to load its definition in the edit window. As an example, if the user selects ```Create ACH File [CreateACHFile]```, the request information would appear as:

![img alt](/img/smaficstemplate3.png)

The selected method is displayed as the first line (```Payments -> Automated -> Create ACH File [CreateACHFile]```). Different types of data are displayed. There are simple items (like ```IncludeLoansWithStops``` or ```Object Item 1```) and groups (like ```Array Value #0```) that contain a col- lection of items.

:::info Note

The group name are not included in the final request file; it is shown only to help the user understand the structure.
:::

Lastly, there are containers (like ```DatesToConvert```) that contain multiple objects (either items or groups).

At any time, the user can click the **Show Generated JSON** button to see what the request file will look like when saved. Before making any changes, the **Show Generated JSON** button shows:

![img alt](/img/smaficstemplate4.png)

:::info Note

There may be *comment* lones at the bottom of the ifle that are used by the tool kit components to maintain the datatype or available selection choices. In this case, the selection choices for ```SendTo``` are:
* ```NotSpecified```
* ```FederalReserceBank```
* ```CorrespondentBank```
:::

![img alt](/img/smaficstemplate5.png)

### Right Click Options

#### On a Container

If the user right-clicks on a container line, a pop-up menu will be displayed offering the choice of ```InsertGroup```.

```InsertGroup``` will create a new group entry immediately below the container line. New items can then be inserted into the new group.

#### On a Group

If the user right-clicks on a group line, a pop-up menu will be displayed offering these choices:
* ```Delete Group```
* ```Duplicate Group```
* ```Insert New Items```

```Delete Item``` will operate as expected; the group (and its descendants) will be removed from the request definition.
```Duplicate Group``` will create a new group containing copies of all of the descendants of the selected group.
```Insert New Item``` will display the new Item entry screen:

![img alt](/img/smaficstemplate6.png)

:::info Note
OpCon Global Properties can be used **ONLY** for datatype setrings and dates. SMAFICSConnector performs the substitution before sending it to the web service.
:::

If the **Commit Change** button is selected (after entering data in ```Item Name``` and ```Item Value```) the new item is created immediately after the selected group.

#### On an Item

If the user right-clicks on an Item line, a pop-up menu is displayed offering these choices:
* ```Delete Item```
* ```Duplicate Item```
* ```Edit Item```
* ```Insert Item```

```Delete Item``` operates as expected; the item is be removed from the request definition. 
```Duplicate Item``` creates a similarly named item (```- COPY``` is appended to the item name) immediately below the selected item. In the following screenshot, the user right-clicked ```CreatePrenoteOnlyFile``` and selected ```Duplicate Item```.

![img alt](/img/smaficstemplate7.png)

Selecting ```Edit Item``` opens an editing window. The user can rename the newly created duplicate of ```CreatePrenoteOnlyFile``` or change the value.

![img alt](/img/smaficstemplate8.png)

The original datatype is preserved.

To create a new item (with a different datatype), select ```Insert Item```. The following edit window displays:

![img alt](/img/smaficstemplate8.png)

:::info Note

OpCon Global Properties can be used only for datatype stings and dates. SMAFICSConnector performs the substitution before sending it to the web service.
:::

If the **Commit Change** button is selected (after entering data in ```Item Nem``` and ```Item Value```), the new item is created immediatly after the selected item.

## Configuration Settings

SMAFICSTemplateEditor has a configuration file so that the UI can connect to your FICS environment. Below is an example of the Configuration File.

```
##############################################
#
#    This configuration file is used by
#    SMAFICSTemplateEditor.
#
##############################################
[General]

[Web Service Connection Parameters]
LoginConnectionName=
MethodDocumentationURL=
SpecialsDocumentationURL=
ExampleBaseURL=
RequestTimeoutInMilliseconds=
```

### LoginConnectionName

Defines the FICS connection name (Database) to connect to. This should match the **LoginConnectionName** specified in SMAFICSConnector.ini file.

### MethodDocumentationURL

Defines the URL that returns documentation for the available web service methods. FICS can supply this information.

### SpecialsDocumentationURL

Defines the URL that returns documentation for the available "special" web service methods. FICS can supply this information. The **LoginConnectionName** will be appended to this URL.

### ExampleBaseURL

Defines the base URL that returns documentation for a specific web service method. FICS can supply this information.

### RequestTimeoutInMilliseconds

Defines the maximum number of milliseconds to wait for the call for the Method documentation to complete. If the call has not completed within this time, the application displays an error message.
