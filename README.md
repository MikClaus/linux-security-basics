# Linux Security Investigation

A beginner cybersecurity project focused on investigating the security of a Linux system.

## Project Overview

The goal of this investigation was to review a Linux system for common security issues and document the findings.

The investigation included:

* User accounts and group memberships
* Running processes
* Network listening services
* Authentication and system logs
* File and directory permissions
* Cron directories
* Basic system hardening and cleanup

## Investigation Steps

### Step 1 — Investigation Setup

Created a separate investigation directory and took a VM snapshot before starting the investigation.

### Step 2 — Users and Groups

Checked local user accounts, login shells, group memberships, and privileged groups such as `sudo`.

**Finding:** No clearly suspicious or unrecognized accounts or unexpected privileged group memberships were identified.

### Step 3 — Processes and Network

Reviewed running processes, checked for processes running from `/tmp`, and checked for network listening ports.

**Finding:** No clearly suspicious processes or unexpected network listeners were identified.

### Step 4 — Authentication and Logs

Reviewed authentication logs for failed and successful login attempts, sudo activity, and APT history.

**Finding:** The reviewed activity did not show clear signs of unauthorized access or unexpected software installation.

### Step 5 — Permissions

Checked permissions for:

* `/etc/passwd`
* `/etc/shadow`
* `/tmp`
* Home directories
* `/etc/cron.d`
* `/var/spool/cron`

**Finding:** No obvious permission problems or world-writable cron files were identified.

### Step 6 — Cleanup and Hardening

No processes or files were removed or modified because the investigation did not identify an issue that required a disruptive change.

## Screenshots

Screenshots of the terminal and some of the commands used during the investigation are included in the `screenshots` directory.

Not every command is shown in the screenshots. Some commands were shortened or modified for the public version to avoid revealing unnecessary system-specific information such as IP addresses and usernames.

No passwords, credentials, private keys, or other sensitive information are intentionally included in this repository.

## Result

Overall, the investigation did not identify any obvious critical security issues on the system.

The project demonstrates a basic Linux security investigation workflow, including collecting evidence, reviewing system configuration and logs, identifying potential issues, and documenting the results.
