# SadServers - Scenario Solutions

We were not able to record video of the solutions. The recording tool displayed errors, such as blacked-out video while only audio played. After 2–3 attempts, the server at https\://sadservers.com/newserver/jakarta displayed a message requiring payment to continue using the service.

![image](https://github.com/user-attachments/assets/7c992616-a6bd-4200-aa57-94a9dfd38054)



This document contains solutions to four SadServers challenges. All problems and solutions are written together in a single file without repeated formatting blocks or section breaks.

## 1. Saint John – Log File Growing

A Python script was writing continuously to /var/log/bad.log, consuming disk space. The task was to stop the process writing to the file without deleting the log itself.

```bash
tail -f /var/log/bad.log
ps aux | grep bad
kill <PID>
```

After identifying the process (/home/admin/badlog.py) using ps, it was terminated with kill. Log growth stopped, confirming the solution.

## 2. The Command Line Murders

This scenario was an investigation puzzle set inside /home/admin/clmystery. The objective was to find the name of the murderer and write it to a specific file. After exploring files and following clues, the murderer was found.

```bash
echo "Jeremy Bowers" > /home/admin/mysolution
md5sum ~/mysolution
```

The expected MD5 hash was 9bba101c7369f49ca890ea96aa242dd5, which confirmed the correct solution.

## 3. Manhattan – Can't Write to PostgreSQL

PostgreSQL was running but inserts were failing. The issue was related to disk space and/or data directory permissions.

```bash
df -h
grep data_directory /etc/postgresql/14/main/postgresql.conf
chown -R postgres:postgres /var/lib/postgresql/14/main
chmod 700 /var/lib/postgresql/14/main
journalctl --vacuum-time=1d
rm -rf /tmp/*
systemctl restart postgresql
sudo -u postgres psql -c "insert into persons(name) values ('jane smith');" -d dt
```

The insert worked after freeing space and correcting permissions. Output was INSERT 0 1.

## 4. Jakarta – It's Always DNS

This server could not resolve DNS names like google.com. The goal was to fix DNS resolution, not connectivity.

```bash
cat /etc/resolv.conf
systemctl restart systemd-resolved
rm /etc/resolv.conf
echo "nameserver 8.8.8.8" > /etc/resolv.conf
ping google.com
```

After manually setting the nameserver to 8.8.8.8, DNS queries started working. The ping command showed IP resolution for google.com, confirming that DNS was fixed.

