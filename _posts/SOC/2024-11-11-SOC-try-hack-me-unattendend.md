---
title: "[SOC-Level 1] Tryhackme - Windows Forensics - Unattendend"
date: 2024-11-11 12:00:00 -500 
categories: [SOC-Level 1 - Tryhackme, Windows Forensics - Unattendend]
tags: [soc,tryhackme,blueteam,windows,forensics]
author: Christoph K
---
[TryHackMe- Unattendend ](https://tryhackme.com/r/room/unattended)


### Introduction


    Welcome to the team, kid. I have something for you to get your feet wet.
    Our client has a newly hired employee who saw a suspicious-looking janitor exiting his office as he was about to return from lunch.
    I want you to investigate if there was user activity while the user was away between 12:05 PM to 12:45 PM on the 19th of November 2022. If there are, figure out what files were accessed and exfiltrated externally.

    You'll be accessing a live system, but use the disk image already exported to the C:\Users\THM-RFedora\Desktop\kape-results\C directory for your investigation. The link to the tools that you'll need is in C:\Users\THM-RFedora\Desktop\tools 

    Finally, I want to remind you that you signed an NDA, so avoid viewing any files classified as top secret. I don't want us to get into trouble.




#### Cheatsheet

![Cheatsheet](assets/img/unattendend/cheatsheet.png)




## Task 1: Snooping around

    Initial investigations reveal that someone accessed the user's computer during the previously specified timeframe.

    Whoever this someone is, it is evident they already know what to search for. Hmm. Curious.


- What file type was searched for using the search bar in Windows Explorer?
- What top-secret keyword was searched for using the search bar in Windows Explorer?



#### 1.1 What file type was searched for using the search bar in Windows Explorer?

For that we use our cheatsheet and check the path for `Search bars`. We use the tool `Registry Explorer`.

We open `Live System` and open the `USERDAT`.

![registry](assets/img/unattendend/registry.png)

And check the path from the `cheatsheet`. 

`NTUSER.DAT\Software\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery`

![01](assets/img/unattendend/unattendend-01.png)


Answer: `.pdf`


#### 1.2 What top-secret keyword was searched for using the search bar in Windows Explorer?

Now we have to check the next Value. 

![02](assets/img/unattendend/unattendend-02.png)

Answer: `continental`


## Task 2: Can`t simply open it 

    Not surprisingly, they quickly found what they are looking for in a matter of minutes.

    Ha! They seem to have hit a snag! They needed something first before they could continue.

    Note:  When using the Autopsy Tool, you can speed up the load times by only selecting "Recent Activity" when configuring the Ingest settings.

- What is the name of the downloaded file to the Downloads folder?
- When was the file from the previous question downloaded? (YYYY-MM-DD HH:MM:SS UTC)
- Thanks to the previously downloaded file, a PNG file was opened. When was this file opened? (YYYY-MM-DD HH:MM:SS)

### Task 2.1  What is the name of the downloaded file to the Downloads folder?


For the next task we use `Autopsy` and open a new case with the local disk. After adding the local `Data Source` we can search the `Download Folder`:

![03](assets/img/unattendend/unattendend-03.png)


Answer: `7z2201-x64.exe`

### Task 2.2  When was the file from the previous question downloaded? (YYYY-MM-DD HH:MM:SS UTC)

For that we go into `Data Artefacts` and check the `Web Downloads`.

![04](assets/img/unattendend/unattendend-04.png)

Answer: `2022-11-19 12:09:19 UTC`

### Task 2.3 Thanks to the previously downloaded file, a PNG file was opened. When was this file opened? (YYYY-MM-DD HH:MM:SS)


We stay in `Data Artefacts` but now we check `Recent Documents` and look for the `.png` file. We could also check the `Registry Explorer` and look into `RecentDocs`



![05](assets/img/unattendend/unattendend-05.png)

Answer: `2022-11-19 12:10:21`


## Task 3: Sending it outside

- A text file was created in the Desktop folder. How many times was this file opened?
- When was the text file from the previous question last modified? (MM/DD/YYYY HH:MM)
- The contents of the file were exfiltrated to pastebin.com. What is the generated URL of the exfiltrated data?
- What is the string that was copied to the pastebin URL?

### Task 3.1 A text file was created in the Desktop folder. How many times was this file opened?

Now we use the tool `JLEcmd`. We bake the following command: 

`JLECmd.exe -d C:\Users\THM-RFedora\Desktop\kape-results\C\Users\THM-RFedora\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations\ --csv . `

Here the help text from `jlecmd`

```bash

C:\tools\JLECmd>JLECmd.exe
Description:
  JLECmd version 1.5.0.0

  Author: Eric Zimmerman (saericzimmerman@gmail.com)
  https://github.com/EricZimmerman/JLECmd

  Examples: JLECmd.exe -f "C:\Temp\f01b4d95cf55d32a.customDestinations-ms" --mp
          JLECmd.exe -f "C:\Temp\f01b4d95cf55d32a.automaticDestinations-ms" --json "D:\jsonOutput" --jsonpretty
          JLECmd.exe -d "C:\CustomDestinations" --csv "c:\temp" --html "c:\temp" -q
          JLECmd.exe -d "C:\Users\e\AppData\Roaming\Microsoft\Windows\Recent" --dt "ddd yyyy MM dd HH:mm:ss.fff"

          Short options (single letter) are prefixed with a single dash. Long commands are prefixed with two dashes


Usage:
  JLECmd [options]

Options:
  -f <f>             File to process. Either this or -d is required
  -d <d>             Directory to recursively process. Either this or -f is required
  --all              Process all files in directory vs. only files matching *.automaticDestinations-ms or
                     *.customDestinations-ms [default: False]
  --csv <csv>        Directory to save CSV formatted results to. This or --json required unless --de or --body is
                     specified
  --csvf <csvf>      File name to save CSV formatted results to. When present, overrides default name
  --json <json>      Directory to save json representation to. Use --pretty for a more human readable layout
  --html <html>      Directory to save xhtml formatted results to. Be sure to include the full path in double quotes
  --pretty           When exporting to json, use a more human readable layout [default: False]
  -q                 Only show the filename being processed vs all output. Useful to speed up exporting to json and/or
                     csv [default: False]
  --ld               Include more information about lnk files [default: False]
  --fd               Include full information about lnk files (Alternatively, dump lnk files using --dumpTo and process
                     with LECmd) [default: False]
  --appIds <appIds>  Path to file containing AppIDs and descriptions (appid|description format). New appIds are added
                     to the built-in list, existing appIds will have their descriptions updated
  --dumpTo <dumpTo>  Directory to save exported lnk files
  --dt <dt>          The custom date/time format to use when displaying timestamps. See https://goo.gl/CNVq0k for
                     options. Default is: yyyy-MM-dd HH:mm:ss [default: yyyy-MM-dd HH:mm:ss]
  --mp               Display higher precision for timestamps [default: False]
  --withDir          When true, show contents of Directory not accounted for in DestList entries [default: False]
  --debug            Show debug information during processing [default: False]
  --trace            Show trace information during processing [default: False]
  --version          Show version information
  -?, -h, --help     Show help and usage information


Either -f or -d is required. Exiting
```


After running our command we get a `csv` file where we can find the necessary information. 

![06](assets/img/unattendend/unattendend-06.png)

Answer: `2`

### Task 3.2 When was the text file from the previous question last modified? (MM/DD/YYYY HH:MM)

It is in the same `csv`: 

![07](assets/img/unattendend/unattendend-07.png)

Answer: `11/19/2022 12:12`

### Task 3.3 The contents of the file were exfiltrated to pastebin.com. What is the generated URL of the exfiltrated data?


We go back to `Autopsy` and check the `Web History`. We can find out, what addresses were used in the recent web history:

![08](assets/img/unattendend/unattendend-08.png)


Answer: `https://pastebin.com/1FQASAav`


### Task 3.4 What is the string that was copied to the pastebin URL?

We can find it here too: 

![08](assets/img/unattendend/unattendend-08.png)

Answer: `ne7AIRhi3PdESy9RnOrN`



## Thoughts

I really enjoy to use `forensic tools` to analyze a client in depth! But I wonder how difficult would it to find anomalies if you dont get asked the questions like in this lab... In total a very nice challenge.. Thank you @tryhackme! 

What did we learn today? 
- using `Registry Forensics` to analyze files and searches
- using `Autopsy` to check downloaded files and `web history`
- using `JLEcmd` to analyze files

