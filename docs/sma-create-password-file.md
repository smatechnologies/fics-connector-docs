---
title: SMACreatePasswordFile
description: "Command-line reference for SMACreatePasswordFile, the utility that encrypts a password and stores it in a file for use in FICS Connector configuration files."
sidebar_label: SMACreatePasswordFile
tags:
  - Reference
  - System Administrator
  - Getting Started
---

# SMACreatePasswordFile

## What is it?

SMACreatePasswordFile is a command-line utility that encrypts a password and stores it in a file. The encrypted file path can then be referenced in FICS Connector configuration files instead of storing a plain-text password.

- Use this utility when setting up FICS Connector configuration files that require a password
- Use this utility to replace any plain-text password stored in a configuration file with an encrypted alternative

:::tip Example

```
SMACreatePasswordFile.exe -file=mypassword.dat -password=<password>
```

:::

## Command line options

| Option | Description |
|---|---|
| `-file` | Defines the name of the file to create that will contain the encrypted password. |
| `-password` | Defines the password to encrypt. |

:::danger

The special characters `` < > | & " ^ ' % `` should be avoided. These characters will not be properly encrypted.

:::

**Related topics:**

- [FICS Connector overview](./overview.md)
- [SMAFICSConnector](./sma-fics-connector.md)
- [Installation](./installation.md)

## FAQs

**Why should I avoid storing passwords in plain text?**

Plain-text passwords in configuration files are a security risk. SMACreatePasswordFile encrypts the password so that the configuration file references a file path rather than the password itself.

**Where do I reference the encrypted password file?**

Specify the path to the encrypted file in the `OpConDBPassword` field of the relevant FICS Connector configuration file (for example, `SMAParseResponseFile.ini`).

**Which special characters are not supported?**

The characters `` < > | & " ^ ' % `` cannot be properly encrypted. Avoid using these in passwords that will be encrypted with this utility.

## Glossary

**Encrypted password file** — A file created by SMACreatePasswordFile that stores a password in an encrypted format. The file path is referenced in FICS Connector configuration files in place of a plain-text password.

**`-file`** — The command-line option that specifies the name and path of the output file where the encrypted password is stored.
