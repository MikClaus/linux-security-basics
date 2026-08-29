# Investigation Notes

## Step 1 — Set Up Investigation Environment

Created the investigation directory:

~/investigation

Created the notes file:

~/investigation/notes.txt

Created VM snapshot:

before-investigation

### Reason

Created a clean workspace and snapshot before starting the investigation.

## Step 2 — Understand Who Is On This System

### 1. User Accounts

Reviewed the user accounts on the system and identified accounts with
interactive login shells.

Command used:

`grep -v nologin /etc/passwd | grep -v false`

### Finding

The command identified **49 accounts** with an interactive login shell.

The accounts were reviewed to identify any unfamiliar or unexpected users.

No clearly suspicious or unrecognized accounts were identified during
the initial review.

### Assessment

The presence of an interactive shell does not necessarily mean that an
account belongs to a human user. Further investigation is required to
determine whether each account is a regular user, administrative account,
or service account.
