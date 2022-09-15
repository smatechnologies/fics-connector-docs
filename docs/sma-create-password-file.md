---
sidebar_label: 'SMACreatePasswordFile'
---

# SMA Create Pasword File

## Overview

Storing a password in plain text in a configuration file should be avoided. This application allows the user to encrypt the password and store it in a file. The name (and path) to the file can be specified in the configuration file.

## Command Line

This is a command line utility that should only be ran via a command prompt. To run this utility, open a command prompt and then execute the following:

```SMACreatePasswordFile.exe -file=mypassword.dat -password=<password>```

| Option | Description |
| -------------- | --------------- |
| -file | Defines the name of the file to create that will contain the encrypted password. |
| -password | Defines the password to encrypt. |

:::danger Warning

The special characters ``` < > | & " ^ ' % ``` should be avoided and will not be properly encrypted.
:::

