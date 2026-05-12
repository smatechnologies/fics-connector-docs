---
title: Installation
description: "System requirements and installation steps for the FICS Connector toolkit on a Windows machine running OpCon."
sidebar_label: Installation
tags:
  - Procedural
  - System Administrator
  - Getting Started
  - Installation
---

# Installation

## What is it?

The FICS Connector is installed on the same machine where OpCon is installed. Running FICS Mortgage Servicer or Mortgage Accountant jobs can be accomplished by using the applications included in this connector. The exposed interface points from FICS are instantiated as web services, and SMA Technologies provides the tools to craft request files and submit them to the appropriate web service interface.

- Use this when you are setting up OpCon to run FICS jobs for the first time
- Use this when you need to verify system requirements before deploying the connector

## Requirements

Any supported version of Windows with .NET Framework 4.5.2 must be installed.

## Install process

To install the FICS Connector, complete the following steps:

1. Download the `SMAFICSConnector.zip` file, which contains the OpCon components required to automate FICS jobs via OpCon.
2. Unzip the connector in the same path where OpCon is installed. You may need to create an `FICS` directory in `ProgramData` if you installed OpCon on the C drive.

:::tip Example

If OpCon is installed on the C drive, your FICS installation is divided across two locations:

- Executables and DLLs: `C:\Program Files\OpConxps\FICS`
- INI files: `C:\ProgramData\FICS`

If OpCon is installed on a non-system drive, your FICS installation is contained in one path:

- Executables, DLLs, and INI files: `D:\OpConxps\FICS`

:::

The FICS Connector is installed and ready for configuration.

**Related topics:**

- [FICS Connector overview](./overview.md)
- [SMAFICSConnector](./sma-fics-connector.md)

## FAQs

**What Windows versions are supported?**

Any supported version of Windows is compatible with the FICS Connector, provided that .NET Framework 4.5.2 is installed.

**Where should I install the connector if OpCon is on a non-system drive?**

If OpCon is installed on a non-system drive (for example, `D:\`), place all connector files — executables, DLLs, and INI files — in a single path such as `D:\OpConxps\FICS`.

**Do I need to create the FICS directory manually?**

You may need to create the `FICS` directory under `ProgramData` if OpCon is installed on the C drive. The installer does not always create this directory automatically.

## Glossary

**.NET Framework 4.5.2** — A Microsoft runtime framework required by the FICS Connector executables. Must be installed on the Windows machine before the connector can run. An in-place update such as 4.6, 4.7, or 4.8 also satisfies this requirement.

**ProgramData** — A Windows system folder (`C:\ProgramData`) used to store application configuration files that apply to all users on the machine. The FICS Connector INI files are stored here when OpCon is installed on the system drive.

**INI file** — A configuration file used by FICS Connector applications to store connection parameters and settings. Stored separately from the executables when OpCon is installed on the system drive.
