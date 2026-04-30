---
title: FICS Connector overview
description: "Overview of the FICS Connector toolkit that enables OpCon to automate FICS Mortgage Servicer and Mortgage Accountant operations through web service integration."
sidebar_label: Overview
tags:
  - Conceptual
  - Automation Engineer
  - Getting Started
---

# FICS Connector overview

## What is it?

The FICS Connector provides connectivity that allows an OpCon job to submit a request to FICS Mortgage Servicer or FICS Mortgage Accountant. The interface points from FICS are exposed as web services, and SMA Technologies provides a toolkit of applications that allow you to craft request files and submit them to the appropriate web service interface.

- Use this connector when you need to automate FICS Mortgage Servicer or Mortgage Accountant operations from within OpCon schedules
- Use this connector to manage dynamic FICS request parameters using OpCon properties and property tokens
- Use this connector to parse FICS web service responses and pass extracted values to downstream OpCon jobs

## Included tools

The FICS Connector toolkit includes the following applications:

| Tool | Type | Purpose |
|---|---|---|
| SMAFICSConnector | Command-line utility | Connects to the FICS API and submits request files to the appropriate web service |
| SMAFICSTemplateEditor | Windows GUI application | Creates and edits FICS request template files while maintaining data types and JSON format integrity |
| SMAParseResponseFile | Command-line utility | Parses saved FICS web service response files to extract tag values for downstream processing |
| SMACreatePasswordFile | Command-line utility | Encrypts passwords for secure storage in configuration files |

**Related topics:**

- [Installation](./installation.md)
- [SMAFICSConnector](./sma-fics-connector.md)
- [SMAFICSTemplateEditor](./sma-fics-template-editor.md)
- [SMAParseResponseFile](./sma-parse-response-file.md)
- [SMACreatePasswordFile](./sma-create-password-file.md)

## FAQs

**What FICS products does this connector support?**

The FICS Connector supports FICS Mortgage Servicer and FICS Mortgage Accountant operations that are exposed as web services.

**Do I need to use SMAFICSTemplateEditor to create request files?**

No. SMAFICSTemplateEditor is optional. You can create and edit request template files in any text editor such as Notepad. However, SMAFICSTemplateEditor maintains data types and JSON format integrity automatically, which reduces formatting errors.

**Can I use OpCon properties in request files?**

Yes. Request files support OpCon User Defined Properties and Schedule Instance properties using the standard `[[Property Name]]` token syntax. System Properties and Managed System Properties are not supported.

**How do I handle FICS operations that require the same timestamp across multiple jobs?**

Create a global OpCon property (for example, `FICS_Timestamp`) and a Null job that sets it at the start of each processing day using an event. Reference `[[FICS_Timestamp]]` in each request file that requires a coordinated timestamp. See [Reference information](./reference.md) for a detailed strategy.

## Glossary

**FICS Mortgage Servicer** — A FICS system that manages mortgage loan servicing operations, accessible through web service interfaces.

**FICS Mortgage Accountant** — A FICS system that manages mortgage accounting operations, accessible through web service interfaces.

**Request file** — A text file containing the formatted JSON parameters required for a FICS web service operation. Request files can be created manually or with SMAFICSTemplateEditor.

**Web service** — The interface provided by FICS through which operations are submitted. SMAFICSConnector communicates with these interfaces to run FICS jobs.

**OpCon property token** — A `[[Property Name]]` placeholder in a request file that OpCon replaces with the property's current value at runtime.
