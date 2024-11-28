A draft PowerShell one-liner to create shares on a server, open PowerShell with admin privileges and copy and paste the below line.

```
New-Item -Path "C:\files" -ItemType Directory -Force; Set-Content -Path "C:\files\readme.txt" -Value "happy golf fish password"; New-Item -Path "C:\files\happy" -ItemType Directory -Force; Set-Content -Path "C:\files\happy\sun.txt" -Value "happy clap golf password"; New-SmbShare -Name "files" -Path "C:\files" -FullAccess "Everyone"; Grant-SmbShareAccess -Name "files" -AccountName "Domain Users" -AccessRight Read -Force

```

This is the response you should see.

```
PS C:\Windows\system32> New-Item -Path "C:\files" -ItemType Directory -Force; Set-Content -Path "C:\files\readme.txt" -Value "happy golf fish password"; New-Item -Path "C:\files\happy" -ItemType Directory -Force; Set-Content -Path "C:\files\happy\sun.txt" -Value "happy clap golf password"; New-SmbShare -Name "files" -Path "C:\files" -FullAccess "Everyone"; Grant-SmbShareAccess -Name "files" -AccountName "Domain Users" -AccessRight Read -Force


    Directory: C:\


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        28/11/2024     12:47                files


    Directory: C:\files


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        28/11/2024     12:47                happy

AvailabilityType      : NonClustered
CachingMode           : Manual
CATimeout             : 0
CompressData          : False
ConcurrentUserLimit   : 0
ContinuouslyAvailable : False
CurrentUsers          : 0
Description           :
EncryptData           : False
FolderEnumerationMode : Unrestricted
IdentityRemoting      : False
Infrastructure        : False
LeasingMode           : Full
Name                  : files
Path                  : C:\files
Scoped                : False
ScopeName             : *
SecurityDescriptor    : O:SYG:SYD:(A;;FA;;;WD)
ShadowCopy            : False
ShareState            : Online
ShareType             : FileSystemDirectory
SmbInstance           : Default
Special               : False
Temporary             : False
Volume                : \\?\Volume{677a5d74-680b-4c27-87bd-20b4f085a124}\
PSComputerName        :
PresetPathAcl         : System.Security.AccessControl.DirectorySecurity


AccessControlType : Allow
AccessRight       : Full
AccountName       : Everyone
Name              : files
ScopeName         : *
PSComputerName    :


AccessControlType : Allow
AccessRight       : Read
AccountName       : HACKLAB\Domain Users
Name              : files
ScopeName         : *
PSComputerName    :



PS C:\Windows\system32>

```

Mounting from a remote host using an account belonging to the domain users group.

```
C:\Users\g.white>pushd \\WIN-5EP48R94F9D\files

Z:\>dir
 Volume in drive Z has no label.
 Volume Serial Number is AA05-C96A

 Directory of Z:\

28/11/2024  20:47    <DIR>          .
28/11/2024  20:47    <DIR>          happy
28/11/2024  20:47                26 readme.txt
               1 File(s)             26 bytes
               2 Dir(s)  49,526,321,152 bytes free

Z:\>

```

Once I get more time I will add a load of vulnerable shares to my https://github.com/myexploit/LAB/blob/master/Hack_Lab_Domain.md script, so you can enumerate for common words across network shares.
