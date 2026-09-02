# Investigation Notes

## Step 1 — Set Up Investigation Environment

First I created a directory for the investigation:

```bash
mkdir ~/investigation
```

Then I created the notes file:

```bash
touch ~/investigation/notes.txt
```

I also created a VM snapshot called:

```text
before-investigation
```

### Reason

I wanted to have a separate place for the investigation files and also have a snapshot of the VM before making any changes.

---

# Step 2 — Understand Who Is On This System

## 1. User Accounts

I checked the user accounts on the system and looked for accounts with an interactive login shell.

Command used:

```bash
grep -v nologin /etc/passwd | grep -v false
```

### Finding

The command showed **49 accounts** with an interactive login shell.

I looked through the accounts to see if there were any users that I did not recognize.

I did not find any clearly suspicious or unknown accounts.

### Assessment

Having an interactive shell does not automatically mean that the account belongs to a real person. Some system or service accounts can also have shells, so this was taken into consideration.

---

## 2. Groups

I checked the system groups and paid particular attention to privileged groups such as `sudo` and `docker`.

### Finding

My user `mickey` is a member of the `sudo` group.

No unexpected privileged group memberships were identified.

---

## 3. Currently Logged-in Users

I checked which users were currently logged into the system.

### Finding

Only my own user session was present.

No unexpected logged-in users were found.

---

## 4. My Account

I checked my own account and its group memberships.

### Finding

Current account:

```text
mickey
```

Groups included:

```text
mickey
sudo
```

The `sudo` membership is expected because this is my administrative account.

---

## 5. Assessment

No unusual user accounts, unexpected privileged group memberships, or unexpected logged-in users were identified.

### Conclusion

No immediate security concerns were identified during this part of the investigation.

The presence of an interactive shell does not necessarily mean that an account belongs to a human user. Further investigation would be needed to determine the purpose of every account.

---

# Step 3 — Processes and Network Listeners

## 1. Full Process List

Command:

```bash
ps aux
```

### Finding

I reviewed the list of currently running processes.

The processes I saw appeared to be related to normal system services, desktop applications and user processes.

I did not identify any clearly suspicious processes during the initial review.

---

## 2. Processes Running from `/tmp`

Command:

```bash
ps aux | grep /tmp
```

### Finding

I checked for processes where `/tmp` appeared in the command or executable path.

No suspicious processes running from `/tmp` were identified.

The `grep /tmp` process itself appeared in the results because it was the command I had just executed. I did not consider this suspicious.

### Assessment

No process needed to be terminated.

---

# Step 4 — Review Authentication and System Logs

## 1. Failed Login Attempts

Command:

```bash
sudo grep -c "Failed password" /var/log/auth.log
```

### Finding

The command counted failed password authentication attempts.

**Result:** 7 failed login attempts.

I reviewed these attempts as part of the investigation to see if they indicated unusual login activity.

### Assessment

The number of failed attempts was small and I did not find evidence from the investigation that they represented an unauthorized login.

---

## 2. Successful Logins

Command:

```bash
sudo grep "Accepted password" /var/log/auth.log
```

### Finding

I reviewed successful password-based login attempts.

**Result:** 4 successful logins.

The successful logins were checked against the expected activity on the machine.

---

## 3. Sudo Usage

Command:

```bash
sudo grep "sudo" /var/log/auth.log
```

### Finding

I reviewed the authentication log for `sudo` usage.

**Result:** 59 matching entries.

The sudo activity was reviewed for unexpected administrative actions.

I did not identify any suspicious sudo activity.

---

## 4. Recent Package Installations

Commands used:

```bash
sudo grep -E "Commandline:|Install:" /var/log/apt/history.log
```

and:

```bash
sudo tail -n 100 /var/log/apt/history.log
```

### Finding

I reviewed the recent APT package installation history.

The history contained entries related to `unattended-upgrades`, which appears to be the normal automatic update mechanism.

**Result:** There were 259 installation-related entries in the reviewed package history. Most appeared to be normal system updates or software installed by me (`mickey`).

### Assessment

I did not identify any unexpected software installations based on the package history that I reviewed.

No changes were made.

---

# Step 5 — Audit Permissions

## Objective

After looking at the historical activity, I checked the current permissions of some important system files and directories.

The main goal was to see if any files could be modified by users who should not have permission to modify them.

---

## 5.1 — `/etc/passwd` and `/etc/shadow`

Command:

```bash
ls -l /etc/passwd /etc/shadow
```

### Results

```text
-rw-r--r-- 1 root root   2828 Aug 11 03:13 /etc/passwd
-rw-r----- 1 root shadow 1332 Aug 11 03:13 /etc/shadow
```

### Expected permissions

For `/etc/passwd`, it is normal for all users to be able to read the file, while only root can write to it.

Expected:

```text
-rw-r--r--
```

For `/etc/shadow`, access should be much more restricted. The exact group permissions can depend on the Linux distribution.

### Assessment

`/etc/passwd` is owned by `root:root`. Root has read and write access and other users have read access only.

`/etc/shadow` is owned by `root:shadow`. Root has read and write access. Members of the `shadow` group have read access, while other users have no access.

The permissions were considered appropriate for this system.

---

## 5.2 — Audit `/tmp`

First I checked the permissions of `/tmp`:

```bash
ls -ld /tmp
```

Result:

```text
drwxrwxrwt 15 root root 340 Sep 1 14:24 /tmp
```

I then checked the contents, including hidden files:

```bash
ls -la /tmp
```

### Assessment

The permissions of `/tmp` are:

```text
drwxrwxrwt
```

The `t` at the end means that the sticky bit is enabled.

This is expected for `/tmp` because multiple users and system services can use this directory. The sticky bit helps prevent users from deleting or renaming files belonging to other users.

The files and directories I saw in `/tmp` appeared to be related to normal desktop and system services, including X11, Snap and systemd.

I did not identify any clearly suspicious files.

---

## 5.3 — Audit Home Directory Permissions

I checked the home directory:

```bash
ls -ld /home/*
```

The directory itself showed permissions consistent with:

```text
drwxr-xr-x
```

The `/home` directory was owned by `root:root`.

### Assessment

The permissions were not world-writable and did not appear to be excessively open.

No obvious permission problem was identified.

---

## 5.4 — Audit Cron Directories

I checked `/etc/cron.d`:

```bash
ls -la /etc/cron.d/
```

I also checked:

```bash
ls -la /var/spool/cron/
```

### Results

The `/etc/cron.d` directory contained entries including:

```text
placeholder
anacron
e2scrub_all
```

These appeared to be normal system cron files.

The `/var/spool/cron` directory contained the `crontabs` directory and was owned by `root:crontab`.

The directory had the sticky bit enabled.

### Assessment

I checked the permissions to see if any cron files were world-writable.

I did not identify an obvious world-writable cron file.

No permission problem was identified that required fixing.

---

## 5.5 — Findings Summary

| Resource          | Expected security state           | Result |
| ----------------- | --------------------------------- | ------ |
| `/etc/passwd`     | Writable only by privileged users | OK     |
| `/etc/shadow`     | Restricted to privileged users    | OK     |
| `/tmp`            | Writable with sticky bit enabled  | OK     |
| `/home/*`         | Not excessively open              | OK     |
| `/etc/cron.d`     | Cron files not world-writable     | OK     |
| `/var/spool/cron` | Cron files not world-writable     | OK     |

### Conclusion

I checked permissions on sensitive system files, `/tmp`, home directories and cron directories.

I did not find a permission problem that needed to be fixed.

---

# Step 6 — Clean Up and Harden

## Objective

After completing the investigation, I checked whether any of the findings required changes to the system.

I did not want to change or delete anything unless there was a reason to do so.

## 6.1 — Suspicious Processes

No suspicious processes running from `/tmp` were identified.

**Action:** No processes were terminated.

**Reason:** There was no suspicious process that required termination.

---

## 6.2 — Unexpected Files in `/tmp`

I reviewed the contents of `/tmp`.

The files and directories appeared to be related to normal system and desktop services.

**Action:** No files were removed.

**Reason:** I did not find any clearly suspicious files, so removing them could potentially affect normal system operation.

---

## 6.3 — Permission Problems

I reviewed permissions on:

```text
/etc/passwd
/etc/shadow
/tmp
/home
/etc/cron.d
/var/spool/cron
```

No permission problem requiring correction was identified.

**Action:** No `chmod` or `chown` commands were used.

**Reason:** The permissions were considered appropriate for the system.

---

## 6.4 — Unexpected Software

The APT history was reviewed for unexpected software.

No clearly unauthorized or unexpected software was identified.

**Action:** No packages were removed.

**Reason:** There was no evidence that a package needed to be removed.

---

## 6.5 — Sudo Access

I checked the sudo membership of the users identified during the investigation.

My account, `mickey`, is a member of the `sudo` group, which is expected because it is my administrative account.

No unauthorized sudo account was identified.

**Action:** No users were removed from the `sudo` group.

**Reason:** No unauthorized sudo access was found.

---

## Conclusion

No cleanup actions were required after the investigation.

I did not find evidence that required me to kill a process, delete a file, change permissions, remove software or remove a user from the `sudo` group.

Because there was no clear reason to make these changes, I left the system unchanged.

Overall, the investigation did not identify any immediate security issue requiring remediation.

## Note: I attached screenshots of the terminal and some of the commands used during the investigation. Not all commands are shown in the screenshots. Some commands were also modified or shortened for the public version in order to avoid revealing private information such as IP addresses and usernames. No passwords, credentials, or other sensitive information are included in the repository.
