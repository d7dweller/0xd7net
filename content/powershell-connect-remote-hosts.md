Title: Configure PowerShell to allow connections to remote systems
Date: 2020-07-20
Category: PowerShell
Tags: windows, powershell, quicktips
Slug: configure-powershell-remote-connections-wsman
Summary: This quicktip covers setting, updating, and deleting the `TrustedHosts` container in the WSMan provider with PowerShell.

Configuring TrustedHosts
-----
Use the `set-item` commandlet to configure `WSman:\localhost\Client\TrustedHosts` to allow authentication to remote hosts:
```powershell
PS C:\Windows\System32> Set-Item WSMan:\localhost\Client\TrustedHosts -Value '192.168.10.20'
```
The below warning will pop up, which essentially states the system will inherently trust whatever System/IP is in `TrustedHosts`. This warning will pop up each time you modify the `TrustedHosts` container.

```text
WinRM Security Configuration.
This command modifies the TrustedHosts list for the WinRM client. The computers in the TrustedHosts list might not be
authenticated. The client might send credential information to these computers. Are you sure that you want to modify this
list?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
```
_____

Verifying the contents of TrustedHosts
---
The content of TrustedHosts can be viewed with the "Get-Item" commandlet:

```powershell
PS C:\Windows\System32> Get-Item WSMan:\localhost\Client\TrustedHosts


   WSManConfig: Microsoft.WSMan.Management\WSMan::localhost\Client

Type            Name                           SourceOfValue   Value
----            ----                           -------------   -----
System.String   TrustedHosts                                   192.168.10.20
```
_____

Adding Additional Hosts to TrustedHosts
---
The `-Concatenate` option can be used to add additional hosts to `TrustedHosts`, instead of overwriting it (which it does by default):
```powershell
PS C:\Windows\System32> set-item WSMan:\localhost\Client\TrustedHosts -Value '*.trusted.domain' -Concatenate
```
Again, you will be warned about the implicit trust of the `TrustedHosts` container.
```text
WinRM Security Configuration.
This command modifies the TrustedHosts list for the WinRM client. The computers in the TrustedHosts list might not be
authenticated. The client might send credential information to these computers. Are you sure that you want to modify this
 list?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
```
Finally, you can verify all entries are present in the `TrustedHosts` container:
```powershell
PS C:\Windows\System32> Get-Item WSMan:\localhost\Client\TrustedHosts


   WSManConfig: Microsoft.WSMan.Management\WSMan::localhost\Client

Type            Name                           SourceOfValue   Value
----            ----                           -------------   -----
System.String   TrustedHosts                                   192.168.10.20,*.trusted.domain
```
_____


Clearing hosts from TrustedHosts
---
Finally, to clear the entries from `TrustedHosts`, simply set the value to nothing:
```powershell
PS C:\Windows\System32> set-Item WSMan:\localhost\Client\TrustedHosts -Value ''
```
```text
WinRM Security Configuration.
This command modifies the TrustedHosts list for the WinRM client. The computers in the TrustedHosts list might not be
authenticated. The client might send credential information to these computers. Are you sure that you want to modify this
 list?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows\System32> Get-Item WSMan:\localhost\Client\TrustedHosts


   WSManConfig: Microsoft.WSMan.Management\WSMan::localhost\Client

Type            Name                           SourceOfValue   Value
----            ----                           -------------   -----
System.String   TrustedHosts
```
_Note_: You must configure the remote system to allow connections, otherwise you will still be unable to connect to the remote system.
_____
Further Reading
-----
[WSMan PowerShell Documenation](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/about/about_wsman_provider?view=powershell-7)
