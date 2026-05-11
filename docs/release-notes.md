---
title: FICS Connector release notes
description: "Version history and change details for the FICS Connector, including new features, improvements, and bug fixes."
sidebar_label: Release notes
tags:
  - Reference
  - Automation Engineer
  - Getting Started
---

# FICS Connector release notes

## 26

### 26.0.1

*05/2026*

This patch release updates the bundled Newtonsoft.Json library to address a high-severity denial-of-service vulnerability.

#### Bug fixes

**Updated the Newtonsoft.Json package to version 13.0.4 to address [CVE-2024-21907](https://nvd.nist.gov/vuln/detail/CVE-2024-21907) / [GHSA-5crp-9r3c-p9vr](https://github.com/advisories/GHSA-5crp-9r3c-p9vr).** This is a CVSS 7.5 (High) denial-of-service vulnerability that affects all Newtonsoft.Json versions prior to 13.0.1. A specially crafted JSON payload passed to the library's JSON deserialization method can trigger a stack overflow and crash the consuming application. The attack is network-reachable and requires no authentication, no elevated privileges, and no user interaction, which means any deployment that deserializes JSON from an untrusted source is exposed. Upgrading the FICS Connector to Newtonsoft.Json 13.0.4 moves the library out of the affected version range and incorporates all subsequent patch fixes.
