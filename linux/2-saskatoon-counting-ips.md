# Saskatoon: Counting IPs

**Difficulty:** Easy  
**OS:** Debian 13

## Problem

Find the IP address that appears most frequently in `/home/admin/access.log` and save it to `/home/admin/highestip.txt`.

## My Approach

Used `cat`, `awk`, `sort`, `uniq`, and `echo`.

```bash
cat /home/admin/access.log | awk '{print $1}' | sort | uniq -c | sort -nr
```
