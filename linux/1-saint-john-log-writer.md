# Saint John — What Is Writing to This Log File?

**Difficulty:** Easy  
**Topic:** Linux processes, logs, troubleshooting

## Problem

A program continuously writes to `/var/log/bad.log`, filling up disk space. Find and terminate the writer without deleting the log file.

## Investigation

### 1. List running processes

```bash
ps aux
```

Found a suspicious process:

```text
admin 589 ... /usr/bin/python3 /home/admin/badlog.py
```

### 2. Search process arguments

```bash
ps aux | grep "/var/log/bad.log"
```

This did not identify the writer because the file path was not present in its command-line arguments.

### 3. Confirm the writer

```bash
lsof -p 589 | grep "/var/log/bad.log"
```

Output:

```text
badlog.py 589 admin 3w REG ... /var/log/bad.log
```

**Meaning:** PID `589` has the target file open for writing. The `3w` field means file descriptor 3 is open for writing.

## Solution

Terminate the confirmed process:

```bash
kill 589
```

Verify the process has stopped:

```bash
ps -p 589 -o pid,stat,cmd
```

Check that the log file size remains unchanged over time:

```bash
stat -c '%s bytes' /var/log/bad.log
```

Check again after waiting longer than the normal write interval.

**Note:** The investigation confirmed PID `589` as the writer. Record the final verification after running the termination and checks.

## Mistakes and Lessons

- `ps aux` shows processes, but not all files they have open.
- Searching process arguments may miss a file opened internally by a program.
- `lsof` helps identify which process has a particular file open.
- Verify the writer before terminating it.
- Do not delete the log file; stop the process writing to it.
- Always verify that the process stopped and the file stopped growing.

## Troubleshooting Pattern

**Symptom → Hypothesis → Evidence → Fix → Verification**
