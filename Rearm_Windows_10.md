

VM Rearm

YOU CAN ONLY REARM WINDOWS 10!!!!!


DOES NOT WORK FOR WIN 11

CMD with admin privs


See how many rearms you got left.

slmgr /dlv

Then to rearm

slmgr /rearm



After 90 days trial period expired, perform the following steps to get additional 240 days trial period.

Press Windows key + R to open a Run box.

Type
regedit
and press Enter to open the Registry Editor. If the UAC (User Account Control) prompt, click Yes to grant admin access.

Navigate to the following location:
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SoftwareProtectionPlatform
```

In the right pane, look for a dword 32-bit registry key called SkipRearm.

Double-click on the SkipRearm registry key and change its value to 1.

Restart your Windows and now you can reset the Windows OS trial period eight more times (8 × 30 days = 240 days)
