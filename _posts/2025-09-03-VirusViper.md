---
title: XINTRA LABS - VirusViper Writeup
categories: [DFIR, XINTRA]
tags: [difir, labs]     # TAG names should always be lowercase
---

<div style="display: flex; align-items: flex-start; gap: 2em;">
  <img src="assets\img\favicons\VVimg\VirusVipersLogo.png" alt="alt text" style="max-width: 250px; height: auto;">
  <div>
    <!-- Qui il tuo testo -->
    <span style="font-size:0.95em;">
      SyntheticPartners is an AI romance chat bot <br>
      company specialising in the production of synthetic partners™. <br>
      They rely on an MSSP company called VirusVipers for all <br>
      their security managed services where access is handled through a VPN. <br>
      Around October 2023, SyntheticPartners was alerted by <br>
      VirusVipers that the MSSP had suffered an internal breach <br>
      by APT10 leading to access of several of their clients networks.<br>
    </span>
  </div>
</div>

# RPD Connection

## Question 1 - Can you identify the timestamp of the suspicious RDP connection SyntheticPartners mentioned?

-------------------------------------

Connection found in the winevent logs:

```
C:\Labs\Evidence\VirusVipers\TriageImages\2023-10-16T070418_Evidence-SP-PC-01\C\Windows\System32\winevt\logs\Security
```

Query the security logs to find RDP connections with logon type 10 (Interactive Logon):

```xml
<QueryList>
  <Query Id="0" Path="file://C:\Labs\Evidence\VirusVipers\TriageImages\2023-10-16T070418_Evidence-SP-PC-01\C\Windows\System32\winevt\logs\Security.evtx">
    <Select Path="file://C:\Labs\Evidence\VirusVipers\TriageImages\2023-10-16T070418_Evidence-SP-PC-01\C\Windows\System32\winevt\logs\Security.evtx">*[System[(EventID=4624) and TimeCreated[@SystemTime&gt;='2023-10-14T00:00:00.000Z' and @SystemTime&lt;='2023-10-14T23:30:00.999Z']]] and *[EventData[Data[@Name='LogonType'] = '10']]</Select>
  </Query>
</QueryList>
```
A single event:

![alt text](/assets/img/favicons/VVimg/VV01.png)

TargetDomainName synpartners

TargetUserName andy.s 

10/14/2023 10:49:20 PM

### Solution 

<details>
  <summary>Spoiler warning</summary>
2023-10-14 22:49:20S
</details>

## Question 2 - What was the source IP address of this specific RDP connection?

-------------------------------------

### Solution

<details>
  <summary>Spoiler warning</summary>
10.0.3.2 (Found in the same log)
</details>

# Patient 0

## Question 3 - What was the patient zero host in this incident?

-------------------------------------

To begin, knowing that the machines have Microsoft Defender, I searched for all detections for the two Virus Viper machines.

And among all, the most interesting one is definitely detected on **VV-PC-01**:

![alt text](assets\img\favicons\VVimg\VV02.png)

Microsoft defender appears to have considered **ImportantDocuments.zip** as a trojan

file:
C:\Users\w.stanley\Downloads\ImportantDocuments.zip
webfile: 
C:\Users\w.stanley\Downloads\ImportantDocuments.zip
https://docs.technicalsupport.info/ImportantDocuments.zip
pid:4088,ProcessStart:133416313587761594

### Solution

<details>
  <summary>Spoiler warning</summary>
VV-PC-01
</details>


## Question 4 - What was the patient zero account in this incident?

-------------------------------------

### Solution

<details>
  <summary>Spoiler warning</summary>
virusvipers\w.stanley (In the same log)
</details>

# Delivering Payload

## Question 5 - What IP was the patient zero account using when they were visiting the Exchange OWA portal?

-------------------------------------

In the Edge sessions I found the email and the path of the OWA application and w.stanley's email:

http://10.1.0.4/owa (VV-EX-01)

![alt text](assets\img\favicons\VVimg\VV03.png)

This discovery can make us answer the nex questioons

### Solution

<details>
  <summary>Spoiler warning</summary>
10.1.0.6
</details>

## Question 6 - What user agent was the patient zero account using when they were visiting the Exchange OWA?

-------------------------------------


To find the user agent, we can retrieve the data directly from the VV-EX-01 Exchange machine

![alt text](assets\img\favicons\VVimg\VV04.png)

### Solution

<details>
  <summary>Spoiler warning</summary>
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML  like Gecko) Chrome/117.0.0.0 Safari/537.36 Edg/117.0.2045.60
</details>

## Question 7 - What is the email address of the threat actor?

-------------------------------------

To answer this question, we can use **Stellar Repair for Exchange** to open the mail db in the Exchange server:

Path:

```
C:\Labs\Evidence\VirusVipers\TriageImages\2023-10-16T054705_Evidence-VV-EX-01\C\Program Files\Microsoft\Exchange Server\V15\Mailbox\Mailbox Database 0132633920\Mailbox Database 0132633920.edb
```
And here is the first email that generated the first Defender alert:

![alt text](assets\img\favicons\VVimg\VV05.png)


![alt text](assets\img\favicons\VVimg\VV06.png)

### Solution

<details>
  <summary>Spoiler warning</summary>
bricktop@securitydocs.awsapps.com
</details>

## Question 8 - What threat name did Windows Defender classify the item that the patient zero account first downloaded?

-------------------------------------

This can be retrieved from the log in question 3

### Solution

<details>
  <summary>Spoiler warning</summary>
Trojan:Script/Wacatac.B!ml
</details>

## Question 9 - What date was the domain registered which hosted the initial payloads?

-------------------------------------

For this we can just put **docs.technicalsupport.info** on <a href="https://www.whois.com/whois/" target="_blank">Whois</a> and look at the registration date

## Solution

<details>
  <summary>Spoiler warning</summary>
2023-10-06
</details>

## Question 10 - What was the password of the second zip file?

-------------------------------------

### Solution

<details>
  <summary>Spoiler warning</summary>
VirusSystems (Found in the user inbox see Question 7)
</details>

# Infection Time

## Question 11 - What is the name of the batch script that the victim ran to initiate the infection?

-------------------------------------

To find this, it is necessary to discover the contents of ImportantDocumentsV2.zip.

To do this, we look for "File created" events involving ImportantDocumentsV2

![alt text](assets\img\favicons\VVimg\VV07.png)

And here we find the files we are interested in and the batch script referred to in the question:

![alt text](assets\img\favicons\VVimg\VV08.png)

### Solution 

<details>
  <summary>Spoiler warning</summary>
  SecureLauncher.bat
</details>

## Question 12 - What is the "key" that is provided to the malicious PowerShell script?

-------------------------------------

To answer this, we can look at the logs of the commands executed by PowerShell on the machine:

Or in the PowerShell logs present in the user's Documents folder:

![alt text](assets\img\favicons\VVimg\VV09.png)


Or, of course, directly in the logs by searching for SecureDocs.ps1 activities:

![alt text](assets\img\favicons\VVimg\VV10.png)

### Solution

<details>
  <summary>Spoiler warning</summary>
  ImportantDocuments
</details>

## Question 13 - What was the malicious domain the PowerShell process attempted to connect to?

-------------------------------------

To find the answer to this question, we can look for event code 22 related to DNS request registrations and link them to powershell.exe processes:

![alt text](assets\img\favicons\VVimg\VV12.png)

We can confirm the domain by analyzing SecureDocs.ps1 more closely, where a huge base64 blob is followed by:

```powershell
$d = xd $key $e 
$a = [System.Convert]::FromBase64String($d) [System.Reflection.Assembly]::Load($a) | Out-Null 
[ImportantDocs.Program]::Main()
```

The fact that it is loading an Assembly in memory and calling the Main() of ImportantDocs makes it clear that we are dealing with a .NET application

To learn more about the file dropped by the ps1, I made it so that instead of being executed, it was written to a file by changing the final part:

```powershell
$path = "C:\Temp\ImportantDocs.exe"; 
New-Item -ItemType Directory -Force -Path (Split-Path $path); 
$d = xd $key $e; $a = [System.Convert]::FromBase64String($d); 
[System.IO.File]::WriteAllBytes($path, $a)
```

And here we find the following hash:

```
B5E81B97F8C874D4843CDE448CC15BFE552807113A11301A36AD3FF32517D8CB
```

![alt text](assets\img\favicons\VVimg\VV11.png)

From what VirusTotal shows, we can therefore trace the file back to [donut loader](https://github.com/TheWover/donut).

If we check the details, we can find the same domain that we saw in the logs:

![alt text](assets\img\favicons\VVimg\VV13.png)

### Solution

<details>
  <summary>Spoiler warning</summary>
  viper.vpn-update.zip
</details>

## Question 14 - What is the PID of the process that first made connect to the malicious C2 on the patient zero host?

-------------------------------------

We can simply retrieve the PID from the log.

### Solution

<details>
  <summary>Spoiler warning</summary>
  8820
</details>

## Question 15 - What was the original file name of the encrypted payload embedded in the .ps1 script?

-------------------------------------

To answer this, we can refer to the analysis done for question 13.

The name can be found on VirusTotal.

### Solution
<details>
  <summary>Spoiler warning</summary>
  Docs.exe
</details>

## Question 16 - Identify the key (in UTF8) in the .NET binary that is used to decrypt the payload from the previous question

-------------------------------------

To answer this, we can use the **ILSpy** tool since our file is a .NET, and here is the key:

![alt text](assets\img\favicons\VVimg\VV14.png)

<details>
  <summary>Spoiler warning</summary>
  US5A3G5FQVV8
</details>

## Question 17 - What is the full path of the process the initial payload spawned and then injected itself into?

-------------------------------------

Looking at the code with ILSpy, we can again notice a base64 blob on which self-injection is performed with CreateThread:

```c
string s = XOR_Decrypt(ciphertext);
byte[] array = Convert.FromBase64String(s);
IntPtr intPtr = VirtualAlloc(0u, (uint)array.Length, 12288u, 4u);
Marshal.Copy(array, 0, intPtr, array.Length);
IntPtr zero = IntPtr.Zero;
uint lpThreadId = 0u;
IntPtr zero2 = IntPtr.Zero;
VirtualProtect(intPtr, (uint)array.Length, 32u, out var _);
zero = CreateThread(0u, 0u, intPtr, zero2, 0u, ref lpThreadId);
WaitForSingleObject(zero, uint.MaxValue);
```

This information allows us to answer the question because, having discovered the PID of the PowerShell script, we just need to set it as ParentProcessId to find out where the new thread was actually spawned.

### Solution

<details>
  <summary>Spoiler warning</summary>
  C:\Windows\System32\rundll32.exe
</details>

## Question 18 - If the inital payload was a PowerShell script, what processes did that spawn?

-------------------------------------

To answer this question again we just have to look at the PID of Rundll32.exe

### Solution

<details>
  <summary>Spoiler warning</summary>
  4776
</details>

