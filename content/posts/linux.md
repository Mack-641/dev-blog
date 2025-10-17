---
title: "Linux Command Cheatsheet"
date: '2025-10-17T17:11:08+02:00'
draft: false
tags: ["linux","cheatsheet","cli"]
---

A compact Linux command reference for daily terminal work — copy commands and adapt them to your environment.

<!--more-->

## Files & directories

```bash
ls -la                # list files with details
cd /path/to/dir       # change directory
pwd                   # print working directory
mkdir -p dir/subdir   # create directories
cp -r src dest        # copy files/directories
mv old new            # move/rename
rm file               # remove file
rm -rf dir            # remove directory (dangerous)
```

## Viewing file contents

```bash
cat file
tac file               # reverse cat
less file              # pager (q to quit)
head -n 50 file
tail -n 100 file
tail -f /var/log/syslog  # follow log
```

## Searching & text processing

```bash
grep -R --line-number "pattern" .
grep -i "pattern" file      # case-insensitive
awk '{print $1,$3}' file
sed -n '1,50p' file
cut -d',' -f1 file
sort file | uniq -c | sort -rn
xargs -0 < list.txt command
```

## File find & permissions

```bash
find /path -type f -name "*.log"
find . -mtime -7            # modified within 7 days
stat file                   # file metadata
chmod 644 file              # set permissions
chown user:group file       # change owner
```

## Processes & system info

```bash
ps aux | grep myproc
pgrep -f process_name
top                        # interactive process viewer
htop                       # improved top (if installed)
kill <pid>
kill -9 <pid>              # force kill
nice -n 10 command         # set priority
renice -n -5 <pid>
```

## Networking

```bash
ip addr show
ss -tulpen                 # sockets (recommended over netstat)
curl -I https://example.com
curl -fsSL https://example.com | bash   # careful with piped scripts
ping -c 4 8.8.8.8
traceroute example.com
dig +short example.com
```

## Package managers (examples)

```bash
# Debian/Ubuntu (apt)
sudo apt update
sudo apt install -y package

# RHEL/CentOS (dnf/yum)
sudo dnf install package

# Arch (pacman)
sudo pacman -Syu package
```

## Services & boot

```bash
systemctl status nginx.service
sudo systemctl start|stop|restart nginx
sudo systemctl enable nginx
journalctl -u nginx -f
journalctl -b --since "2 hours ago"
```

## Disks & mount

```bash
df -h                     # disk usage
du -sh ./path             # directory size
lsblk                     # block devices
mount /dev/sdb1 /mnt/data
umount /mnt/data
```

## SSH & remote

```bash
ssh user@host
ssh -i ~/.ssh/key user@host
scp file user@host:/path
rsync -avz ./dir/ user@host:/path
ssh-copy-id user@host
```

## Containers & virtualization (quick)

```bash
docker ps
docker logs -f container
podman ps
virsh list --all
```

## Shell & productivity tips

```bash
# quick file editing
nano file
code file                # VS Code from terminal

# history and re-run
history | grep deploy
!123                     # run history item 123
!!                       # repeat last command

# useful aliases (add to ~/.bashrc or ~/.zshrc)
alias ll='ls -la'
alias gs='git status'
```

## Safety & debugging

- Use sudo only when necessary.
- Preview destructive commands with echo or --dry-run (when available).
- Redirect stderr to a file for debugging: command 2>errors.log
- Use shellcheck for shell script linting.

## Quick checklist before executing scripts from the web

- Inspect the script
- Run in a safe environment or container
- Check required privileges and side effects

That's it — keep this post as a quick Linux CLI reference and extend with distro-specific notes as needed.