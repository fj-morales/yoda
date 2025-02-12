---
parent: Development
title: Development tips
nav_order: 4
---
# Development tips

A collection of tips to make Yoda development easier.

## General

Watch latest iRODS log without unnecessary noise (as user irods):
```bash
ls -t /var/lib/irods/log/rodsLog* | head -n1 | xargs -n 1 -- tail -f | grep -v "{rods#tempZone} Agent process started from 127.0.0.1"
```

Watch flake8 check on Python code:
```bash
watch flake8
```

Run flake8 check on source file change (requires the `entr` package):
```bash
ls *py | entr flake8
```

Reload Flask on project change (requires the `entr` package; run as root):
```bash
cd /var/www/yoda && find /etc/irods/yoda-ruleset . \( -path *.swp -o -path */node_modules/* -o -path ./venv -o -path ./.git \) -prune -o -print | entr touch yoda_debug.wsgi
```

Rebuild portal Javascript assets on source file change:
```bash
./node_modules/.bin/webpack -d -w
```

## Mailpit

The development environments have [Mailpit](https://github.com/axllent/mailpit) for testing email during development.
In order to see what messages Yoda would have sent, browse to port 8025 on the iCAT or EUS server of the environment.

![Mailpit screenshot](screenshot-mailpit.png)

## Datarequest module
Remove all existing data requests (to declutter your _development_ environment):
```bash
icd /tempZone/home/datarequests-research && ils | grep \ \  | sed 's/\ \ C-\ //' | xargs -I COLLPATH sh -c "ichmod -M -r own rods COLLPATH && irm -r COLLPATH"
```
