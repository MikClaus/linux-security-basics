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

### 2. Groups

Reviewed the system groups and checked privileged groups, including
sudo and docker.

### Finding
mickey is a member of sudo.
No unexpected privileged group memberships were identified.
### 3. Currently Logged-in Users

Checked the currently logged-in users.

### Finding

Only my own user session was identified.

No unexpected logged-in users were found.

### 4. My Account

Checked my own account and group memberships.

### Finding

Current account:

mickey

Group memberships include:

mickey
sudo

The sudo membership is expected because this is my administrative account.

### 5. Assessment

No unusual user accounts, unexpected privileged group memberships, or
unexpected logged-in users were identified.

### Conclusion

No immediate security concerns were identified during this part of the
investigation.

The presence of an interactive shell does not necessarily mean that an
account belongs to a human user. Further investigation is required to
determine whether each account is a regular user, administrative account,
or service account.
