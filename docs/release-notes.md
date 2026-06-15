---
title: FICS Connector release notes
description: "Version history and change details for the FICS Connector, including new features, improvements, and bug fixes."
sidebar_label: Release notes
tags:
  - Reference
  - Automation Engineer
  - Upgrade
---

# FICS Connector release notes

## 26

### 26.0.1

*05/2026*

This patch release standardizes every FICS Connector component on .NET Framework 4.5.2 and updates the bundled Newtonsoft.Json library to address a high-severity denial-of-service vulnerability.

#### Bug fixes

**Updated the bundled Newtonsoft.Json library to version 13.0.4 across every FICS Connector component to address [CVE-2024-21907](https://nvd.nist.gov/vuln/detail/CVE-2024-21907) / [GHSA-5crp-9r3c-p9vr](https://github.com/advisories/GHSA-5crp-9r3c-p9vr).** This is a CVSS 7.5 (High) denial-of-service vulnerability that affects all Newtonsoft.Json versions prior to 13.0.1. A specially crafted JSON payload passed to the library's JSON deserialization method can trigger a stack overflow and crash the consuming application. The attack is network-reachable and requires no authentication, no elevated privileges, and no user interaction, which means any deployment that deserializes JSON from an untrusted source is exposed. The FICS Connector, SMA FICS Template Editor, SMA Parse Response File, and the supporting internal libraries are all now on the patched 13.0.4 release. Prior releases shipped with Newtonsoft.Json 10.0.3, which is within the affected version range.

#### Improvements

**Standardized every FICS Connector component on .NET Framework 4.5.2.** Components were previously built against a mix of .NET Framework 4.5, 4.5.2, and 4.6.1, which created inconsistent build and runtime requirements. Aligning every component on 4.5.2 simplifies the supported configuration, removes a stale 4.6.1 target that no longer reflected the code in use, and lets the build pipeline drop older targeting packs.

#### Upgrade notes

**Raised the minimum .NET Framework requirement from 4.5 to 4.5.2.** Before upgrading to 26.0.1, confirm that .NET Framework 4.5.2 (or a later in-place update such as 4.6, 4.7, or 4.8) is installed on every Windows machine that runs the FICS Connector, the SMA FICS Template Editor, the SMA Parse Response File utility, or the SMA Create Password File utility. Machines that already meet the previous 4.5 requirement via a 4.5.2 or newer in-place update need no further action.
