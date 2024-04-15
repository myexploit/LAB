**Creating a hack lab domain**

Tested on server 2008R2, server 2019 and Server 2022.

# For server 2008 - The following one line will convert a server 2008 R2 to a domain controller. Right click on CMD and run as administrator, then copy and paste the single line below in one go. (Clearly read it before but it will set up the domain called hacklab.local).  

```
dcpromo /unattend /InstallDns:yes /dnsOnNetwork:yes /replicaOrNewDomain:domain /newDomain:forest /newDomainDnsName:hacklab.local /DomainNetbiosName:hacklab /databasePath:"c:\Windows\ntds" /logPath:"c:\Windows\ntdslogs" /sysvolpath:"c:\Windows\sysvol" /safeModeAdminPassword:Passw0rd! /forestLevel:2 /domainLevel:2 /rebootOnCompletion:yes
```

# For server 2019 - A PS one-liner to convert your server 2019 into a lab DC.

```
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force ; Install-WindowsFeature AD-Domain-Services  ; Import-Module ADDSDeployment ; Install-ADDSForest  -DatabasePath "C:\Windows\NTDS"  -DomainMode "Win2008R2"  -DomainName "hacklab.local"  -DomainNetbiosName "HACKLAB"  -ForestMode "Win2008R2"  -InstallDns:$true  -LogPath "C:\Windows\NTDS"  -NoRebootOnCompletion:$true  -SysvolPath "C:\Windows\SYSVOL"  -Force:$true ; Add-WindowsFeature RSAT-AD-Tools ; Restart-Computer
```

# Set up a static IP on server 2019

```
New-NetIPAddress –InterfaceAlias Ethernet0 –IPAddress ADD-Your-IP-Address-Here –PrefixLength 24 -DefaultGateway ADD-Your-DG-IP-Address-Here ; Set-DnsClientServerAddress -InterfaceAlias Ethernet0 -ServerAddresses ADD-Your-DNS-IP-Address-Here ; Restart-Computer
```

# Build the fake AD lab

# After a reboot, right click on powershell and run as administrator, then copy the below sections and paste in. 
# This will create OU, and assign users to the department names, each password which is very weak is the same, this is simply to create a lab domain for hacking, feel free to edit each password in the script as you require. It will also set up Service Principal Name (SPN) for some accounts, so you can kerberoast them.

```
# Add Departments organizational unit (OU) Add Head_Office OU with nested department OU and IT OU.

dsadd ou ou=Departments,dc=hacklab,dc=local 
dsadd ou "ou=IT,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Admins,ou=IT,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Service_Accounts,ou=IT,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Help_Desk,ou=IT,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Head_Office,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=HR,ou=Head_Office,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Sales,ou=Head_Office,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Accounts,ou=Head_Office,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Research,ou=Head_Office,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Reception,ou=Head_Office,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Administration,ou=Head_Office,ou=Departments,dc=hacklab,dc=local"
dsadd ou "ou=Senior_Management,ou=Head_Office,ou=Departments,dc=hacklab,dc=local"

# Create a user groups OU

dsadd ou ou=Groups,ou=Departments,dc=hacklab,dc=local

# Create the following user groups to the group OU

dsadd group cn=sales,ou=Groups,ou=Departments,dc=hacklab,dc=local
dsadd group cn=administration,ou=Groups,ou=Departments,dc=hacklab,dc=local
dsadd group cn=accounts,ou=Groups,ou=Departments,dc=hacklab,dc=local
dsadd group cn=help_desk,ou=Groups,ou=Departments,dc=hacklab,dc=local
dsadd group cn=support,ou=Groups,ou=Departments,dc=hacklab,dc=local
dsadd group cn=RDP,ou=Groups,ou=Departments,dc=hacklab,dc=local

# Create Lab Test accounts

# Head_Office / Accounts

dsadd user "cn=n.collins, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.davidson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.davies, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.dawson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.dixon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.edwards, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.elliot, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.evans, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.fisher, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=v.fletcher, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.ford, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.foster, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.fox, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.gibson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.graham, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.grant, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.gray, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.green, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.smith, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Dragon1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.johnason, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Baseball1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.thomas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Abc1231 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.miller, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Football1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.johnsson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Monkey1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.williams, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Letmein1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.harris, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Shadow1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=i.jackson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Qwertyuiop1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.wilsson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Mustang1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.mmoore, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Michael1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.martsinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Superman1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.marjtinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Fuckyou1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=n.anderson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Qazwsx1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.thompson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Killer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.thompson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Trustno11 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.lewis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Jordan1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.robinson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Jennifer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.sancshez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Zxcvbnm1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.clark, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Asdfgh1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.hernandez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Hunter1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=v.hill, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Buster1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.king, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Soccer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.rossi, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Harley1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.darrdvis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Andrew1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.perez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Tigger1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.white, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Sunshine1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.jackson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Iloveyou1 -mustchpwd no -pwdneverexpires yes -desc "Changed the users password to Iloveyou1"
dsadd user "cn=c.smith, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Fuckme1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.taylor, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Charlie1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.martin, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Robert1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.thoffmas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Thomas1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.hernandez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Hockey1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=h.rodrgviguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Ranger1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=i.johncson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Daniel1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=j.miller, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Starwars1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.jones, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Klaster1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.davsris, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd George1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.andessrson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Computer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.johnfson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Michelle1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.mooore, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Jessica1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.clark, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Pepper1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.thomdas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Zxcvbn1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.martianez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Freedom1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.wiloson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passmeup1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.robinson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Fuckoff1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.marteinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Maggie1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=v.sancahez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Aaaaaa1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.moorre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Ginger1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.thompson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Princess1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.martsinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Joshua1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.hernandez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Cheese1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.miller, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Amanda1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.rodriseguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Summer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.anderson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Loveyou1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.sancahez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Ashley1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.wilison, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Nicole1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.davrtsis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Chelsea1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.mooree, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Biteme1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=h.thomddfas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Matthew1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.johnsson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Access1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=j.martainez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Yankees1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.rodrigfduez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Dallas1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.sanchdez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Austin1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.clark, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Thunder1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=n.davdemis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Taylor1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.wilwson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Matrix1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.robinson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd William1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.hernandez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Corvette1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.martiynez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Martin1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.anderson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Heather1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.johnsron, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Secret1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.rodrigkjuez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Fucker1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=v.sancghez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Merlin1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.wilsaon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Diamond1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.davifis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Gfhjkm1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.moossre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Hammer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.thomssas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Silver1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.martinuez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Anthony1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.hernandez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Justin1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.robinson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Bailey1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.clark, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Q1w2e3r4t51 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.jodhnson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Patrick1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.sanwchez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Internet1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.wilpson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Scooter1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=h.davxris, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Orange1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=i.moofrre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Golfer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=j.massrtainez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Cookie1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.rodrijguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Richard1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.sancahez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Samantha1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.anderson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Bigdog1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=n.johnsson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Guitar1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.martiwnez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Jackson1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.hernandez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Whatever1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.wiloson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Mickey1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.davirws, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Chicken1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.moewore, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Sparky1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.thoweermas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Snoopy1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.johnslon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Maverick1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=v.martienez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Phoenix1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.rodrisguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Camaro1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.sanchgez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Peanut1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.wilison, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Morgan1 -mustchpwd no -pwdneverexpires yes -desc "Changed the users password to Morgan1"
dsadd user "cn=z.davsdis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Welcome1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.clark, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Falcon1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.johndson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Cowboy1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.martiwnez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Ferrari1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.rodrigruez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Samsung1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.sanchjez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Andrea1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.wilyson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Smokey1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.davioos, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Steelers1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=h.mooeere, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Joseph1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=i.thomas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Mercedes1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=j.johnhson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Dakota1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.martiunez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Arsenal1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.rodrigiuez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Eagles1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.sanychez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Melissa1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=n.wiwlson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Boomer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.daviuus, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Booboo1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.moorrre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Spider1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.thdddomas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Nascar1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.johntson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Tigers1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.marttinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Yellow1 -mustchpwd no -pwdneverexpires yes -desc "Changed the users password to Yellow1"
dsadd user "cn=t.rodrieguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Gateway1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.sancrhez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Marina1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=v.wilsion, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Diablo1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.davccis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Bulldog1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.moowsxre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Qwer12341 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.thomeeeas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Compaq1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.johnqson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Purple1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.martwinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Hardcore1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.rodrigutuez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Banana1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.sanczhez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Junior1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.wilsuion, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Hannah1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.daerfvis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Porsche1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.mooure, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Lakers1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.thomeeeas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Iceman1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=h.johnsson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Cowboys1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=i.martinwez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd London1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=j.rodrwyiguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Tennis1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.sanchiez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Ncc17011 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.wilyson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Coffee1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.davssis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Scooby1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=n.moorcre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Miller1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.thomderas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Boston1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.johnsson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Q1w2e3r41 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.maratinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Fuckoff1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.rodrieyguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Brandon1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.sancyhez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Yamaha1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.wilseon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Chester1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.daytvis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Mother1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=v.mocdore, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Forever1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.thomattts, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Johnny1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.johnsaon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Edward1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.martihnez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Oliver1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.rodrirtguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Redsox1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.sanchtez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Player1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.wilswon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Nikita1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.davyis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Knight1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.moodsre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Fender1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.thomfffas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Midnight1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.johnso, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Please1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.martinjez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Brandy1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=h.rodrigwuez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Badboy1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=i.sancohez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Iwantu1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=j.wilesosn, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Slayer1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.dawerris, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Rangers1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.moouiyre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Charles1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.thogghmas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Flower1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=n.johseeson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Bigdaddy1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.martidnez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Wizard1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.rodrasiguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Bigdick1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.sanchpez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Jasper1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.wilsson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Rachel1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=s.daveeris, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Steven1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=t.moodce, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Winner1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=u.thomhhas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Adidas1 -mustchpwd no -pwdneverexpires yes -desc "Changed the users password to Adidas1"
dsadd user "cn=v.jhnson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Victoria1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=w.martfinez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Natasha1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=x.rodrifguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Jasmine1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=y.sancuhez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Winter1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=z.wilsaon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Prince1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=a.davihhuus, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Panties1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=b.mootfre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Marine1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=c.thomhhsas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Ghbdtn1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=d.johnsaon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Fishing1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=e.martidfnez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Cocacola1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=f.rodridfguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Casper1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=g.sancthez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Raiders1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=h.wilzson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Marlboro1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=i.davffis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Gandalf1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=j.moodckre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Asdfasdf1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=k.thomeweas, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Crystal1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=l.johnon, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -pwdneverexpires yes
dsadd user "cn=m.martiynez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Golden1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=n.rodrsiguez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Blowme1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=o.sanrchez, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Bigtits1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=p.wiltson, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Panther1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=q.davfwfis, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Lauren1 -mustchpwd no -pwdneverexpires yes
dsadd user "cn=r.mooyre, ou=Accounts, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Angela1 -mustchpwd no -pwdneverexpires yes

# Head_Office / Administration

dsadd user "cn=m.jenkins, ou=Administration, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=n.johnson, ou=Administration, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=o.jones, ou=Administration, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=g.white, ou=Administration, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=h.yalden, ou=Administration, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=i.yarbury, ou=Administration, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=j.yardley, ou=Administration, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no

# Head_Office / HR

dsadd user "cn=z.mcdonald, ou=HR, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=a.murphy, ou=HR, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=b.natt, ou=HR, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=c.nelson, ou=HR, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=d.nightingale, ou=HR, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=e.nixon, ou=HR, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=f.nutter, ou=HR, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no

# Head_Office / Reception

dsadd user "cn=p.kelly, ou=Reception, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=q.kennedy, ou=Reception, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=u.king, ou=Reception, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=r.knight, ou=Reception, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=s.lawrence, ou=Reception, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=t.lee, ou=Reception, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no

# Head_Office / Research

dsadd user "cn=u.lewis, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=v.lloyd, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=w.marshall, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=x.martin, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=y.mason, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=g.dell, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=h.osborne, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=i.owen, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=j.oxley, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=k.page, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=l.painter, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=m.palmer, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=n.pastor, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=o.peterson, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=p.quill, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=q.quimby, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=u.quintrell, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=r.ramsey, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=s.ratliff, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=t.richards, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=u.roberts, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=v.robinson, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=w.scott, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=x.simpson, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=y.smith, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=z.stewart, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=a.taylor, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=b.turner, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=c.walsh, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=d.ward, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=e.webb, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=f.west, ou=Research, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no

# Head_Office / Sales

dsadd user "cn=d.atkinson, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Summer123 -mustchpwd no
dsadd user "cn=e.bailey, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=f.baker, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=g.ball, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=h.bell, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=i.brown, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=j.burton, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=k.carter, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=l.clarke, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=m.cole, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=e.griffiths, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=f.hall, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=g.hamilton, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=h.harris, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=i.harvey, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=j.hill, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=k.jackson, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=l.james, ou=Sales, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no

# Head_Office / Senior_Management

dsadd user "cn=k.yarrow, ou=Senior_Management, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=l.yates, ou=Senior_Management, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=m.young, ou=Senior_Management, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=n.zachary, ou=Senior_Management, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=o.zelly, ou=Senior_Management, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=p.zinc, ou=Senior_Management, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=q.zouch, ou=Senior_Management, ou=Head_Office, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no

# Head_Office / Help_Desk

dsadd user "cn=a.adams, ou=Help_Desk, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=b.allen, ou=Help_Desk, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no
dsadd user "cn=c.armstrong, ou=Help_Desk, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no

# Admins / IT / DA

dsadd user "cn=adm.adams, ou=Admins, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=adm.smith, ou=Admins, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=adm.stewart, ou=Admins, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=adm.natt, ou=Admins, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=adm.nelson, ou=Admins, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"

# Service Accounts / IT 

dsadd user "cn=svc_afds, ou=Service_Accounts, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=svc_test, ou=Service_Accounts, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=svc_mssql1, ou=Service_Accounts, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=svc_mssql2, ou=Service_Accounts, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=svc_lab, ou=Service_Accounts, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"
dsadd user "cn=svc_admin, ou=Service_Accounts, ou=IT, ou=Departments, dc=hacklab, dc=local" -fn User -ln test -pwd Passw0rd! -mustchpwd no -memberof "CN=Domain Admins,CN=Users,dc=hacklab, dc=local"

# Set up Service Principal Name (SPN) for the following accounts so you can kerberoast them.

setspn -s http/server1.hacklab.local:8082 svc_afds
setspn -s http/server1.hacklab.local:8083 svc_test
setspn -s http/server1.hacklab.local:8084 svc_mssql1
setspn -s http/server1.hacklab.local:8085 svc_mssql2
setspn -s http/server1.hacklab.local:8086 svc_lab
setspn -s http/server1.hacklab.local:8087 svc_admin

# Make the following accounts vulnerable to asreproast.

Set-ADAccountControl -Identity m.jenkins -DoesNotRequirePreAuth 1
Set-ADAccountControl -Identity z.mcdonald -DoesNotRequirePreAuth 1
Set-ADAccountControl -Identity u.lewis -DoesNotRequirePreAuth 1

# Create a description filed with a password in it.

Set-ADUser d.atkinson -Description "User Password Summer123"

# Disable SMB Signing on the DC.

Set-SmbClientConfiguration -RequireSecuritySignature 0 -EnableSecuritySignature 0 -Confirm -Force

# Add Domain Machines

New-ADComputer -Name "SR2000-1" -SamAccountName "SR2000-1" -Enabled $True -OperatingSystem "Windows Server 2000 Service Pack 4"
New-ADComputer -Name "SR2000-2" -SamAccountName "SR2000-2" -Enabled $True -OperatingSystem "Windows Server 2000 Service Pack 4"
New-ADComputer -Name "SR2000-3" -SamAccountName "SR2000-3" -Enabled $True -OperatingSystem "Windows Server 2000 Service Pack 4"
New-ADComputer -Name "SR2000-4" -SamAccountName "SR2000-4" -Enabled $True -OperatingSystem "Windows Server 2000 Service Pack 4"
New-ADComputer -Name "SR2000-5" -SamAccountName "SR2000-5" -Enabled $True -OperatingSystem "Windows Server 2000 Service Pack 4"
New-ADComputer -Name "SR2000-6" -SamAccountName "SR2000-6" -Enabled $True -OperatingSystem "Windows Server 2000 Service Pack 4"
New-ADComputer -Name "SR2003-1" -SamAccountName "SR2003-1" -Enabled $True -OperatingSystem "Windows Server 2003 Datacenter Service Pack 2"
New-ADComputer -Name "SR2003-2" -SamAccountName "SR2003-2" -Enabled $True -OperatingSystem "Windows Server 2003 Datacenter Service Pack 2"
New-ADComputer -Name "SR2003-3" -SamAccountName "SR2003-3" -Enabled $True -OperatingSystem "Windows Server 2003 Datacenter Service Pack 2"
New-ADComputer -Name "SR2003-4" -SamAccountName "SR2003-4" -Enabled $True -OperatingSystem "Windows Server 2003 Datacenter Service Pack 2"
New-ADComputer -Name "SR2003-5" -SamAccountName "SR2003-5" -Enabled $True -OperatingSystem "Windows Server 2003 Datacenter Service Pack 2"
New-ADComputer -Name "SR2003-6" -SamAccountName "SR2003-6" -Enabled $True -OperatingSystem "Windows Server 2003 Datacenter Service Pack 2"
New-ADComputer -Name "SR2008-1" -SamAccountName "SR208-1" -Enabled $True -OperatingSystem "Windows Server 2008 R2 Standard Service Pack 1"
New-ADComputer -Name "SR2008-2" -SamAccountName "SR208-2" -Enabled $True -OperatingSystem "Windows Server 2008 R2 Standard Service Pack 1"
New-ADComputer -Name "SR2008-3" -SamAccountName "SR208-3" -Enabled $True -OperatingSystem "Windows Server 2008 R2 Standard Service Pack 1"
New-ADComputer -Name "SR2008-4" -SamAccountName "SR208-4" -Enabled $True -OperatingSystem "Windows Server 2008 R2 Standard Service Pack 1"
New-ADComputer -Name "SR2008-5" -SamAccountName "SR208-5" -Enabled $True -OperatingSystem "Windows Server 2008 R2 Standard Service Pack 1"
New-ADComputer -Name "SR2008-6" -SamAccountName "SR208-6" -Enabled $True -OperatingSystem "Windows Server 2008 R2 Standard Service Pack 1"
New-ADComputer -Name "SR2012-1" -SamAccountName "SR2012-1" -Enabled $True -OperatingSystem "Windows Server 2012 Standard"
New-ADComputer -Name "SR2012-2" -SamAccountName "SR2012-2" -Enabled $True -OperatingSystem "Windows Server 2012 Standard"
New-ADComputer -Name "SR2012-3" -SamAccountName "SR2012-3" -Enabled $True -OperatingSystem "Windows Server 2012 Standard"
New-ADComputer -Name "SR2012-4" -SamAccountName "SR2012-4" -Enabled $True -OperatingSystem "Windows Server 2012 Standard"
New-ADComputer -Name "SR2019-1" -SamAccountName "SR2019-1" -Enabled $True -OperatingSystem "Windows Server 2019 Standard"
New-ADComputer -Name "SR2019-2" -SamAccountName "SR2019-2" -Enabled $True -OperatingSystem "Windows Server 2019 Standard"
New-ADComputer -Name "SR2019-3" -SamAccountName "SR2019-3" -Enabled $True -OperatingSystem "Windows Server 2019 Standard"
New-ADComputer -Name "SR2019-4" -SamAccountName "SR2019-4" -Enabled $True -OperatingSystem "Windows Server 2019 Standard"
New-ADComputer -Name "W7-1" -SamAccountName "W7-1" -Enabled $True -OperatingSystem "Windows 7 Professional Service Pack 1"
New-ADComputer -Name "W7-2" -SamAccountName "W7-2" -Enabled $True -OperatingSystem "Windows 7 Professional Service Pack 1"
New-ADComputer -Name "W7-3" -SamAccountName "W7-3" -Enabled $True -OperatingSystem "Windows 7 Professional Service Pack 1"
New-ADComputer -Name "W7-4" -SamAccountName "W7-4" -Enabled $True -OperatingSystem "Windows 7 Professional Service Pack 1"
New-ADComputer -Name "W7-5" -SamAccountName "W7-5" -Enabled $True -OperatingSystem "Windows 7 Professional Service Pack 1"
New-ADComputer -Name "W7-6" -SamAccountName "W7-6" -Enabled $True -OperatingSystem "Windows 7 Professional Service Pack 1"
New-ADComputer -Name "XP-1" -SamAccountName "XP-1" -Enabled $True -OperatingSystem "Windows XP Service Pack 1"

# Set UP ACL's

Import-Module ActiveDirectory
Set-Location AD:

Function SetAcl($for, $to, $right, $inheritance)
{
    $forSID = New-Object System.Security.Principal.SecurityIdentifier (Get-ADUser $for).SID
    $objOU = ($to).DistinguishedName
    $objAcl = get-acl $objOU
    # https://docs.microsoft.com/fr-fr/dotnet/api/system.directoryservices.activedirectoryrights?view=dotnet-plat-ext-5.0
    $adRight =  [System.DirectoryServices.ActiveDirectoryRights] $right # https://docs.microsoft.com/fr-fr/dotnet/api/system.directoryservices.activedirectoryrights?view=dotnet-plat-ext-5.0
    $type =  [System.Security.AccessControl.AccessControlType] "Allow" # https://docs.microsoft.com/fr-fr/dotnet/api/system.security.accesscontrol.accesscontroltype?view=dotnet-plat-ext-5.0
    $inheritanceType = [System.DirectoryServices.ActiveDirectorySecurityInheritance] $inheritance # https://docs.microsoft.com/fr-fr/dotnet/api/system.directoryservices.activedirectorysecurityinheritance?view=dotnet-plat-ext-5.0
    $ace = New-Object System.DirectoryServices.ActiveDirectoryAccessRule $forSID,$adRight,$type,$inheritanceType
    $objAcl.AddAccessRule($ace)
    Set-Acl -AclObject $objAcl -path $objOU
}


Function SetAclExtended($for, $to, $right, $extendedRightGUID, $inheritance)
{
    $forSID = New-Object System.Security.Principal.SecurityIdentifier (Get-ADUser $for).SID
    $objOU = ($to).DistinguishedName
    $objAcl = get-acl $objOU
    # https://docs.microsoft.com/fr-fr/dotnet/api/system.directoryservices.activedirectoryrights?view=dotnet-plat-ext-5.0
    $adRight =  [System.DirectoryServices.ActiveDirectoryRights] $right # https://docs.microsoft.com/fr-fr/dotnet/api/system.directoryservices.activedirectoryrights?view=dotnet-plat-ext-5.0
    $type =  [System.Security.AccessControl.AccessControlType] "Allow" # https://docs.microsoft.com/fr-fr/dotnet/api/system.security.accesscontrol.accesscontroltype?view=dotnet-plat-ext-5.0
    $inheritanceType = [System.DirectoryServices.ActiveDirectorySecurityInheritance] $inheritance # https://docs.microsoft.com/fr-fr/dotnet/api/system.directoryservices.activedirectorysecurityinheritance?view=dotnet-plat-ext-5.0

    $ace = New-Object System.DirectoryServices.ActiveDirectoryAccessRule $forSID,$adRight,$type,$extendedRightGUID,$inheritanceType
    $objAcl.AddAccessRule($ace)
    Set-Acl -AclObject $objAcl -path $objOU
}

## acl values :
# AccessSystemSecurity
# CreateChild
# Delete
# DeleteChild
# DeleteTree
# ExtendedRight
# GenericAll
# GenericExecute
# GenericRead
# GenericWrite
# ListChildren
# ListObject
# ReadControl
# ReadProperty
# Self
# Synchronize
# WriteDacl
# WriteOwner
# WriteProperty 

## extend rights
# "00299570-246d-11d0-a768-00aa006e0529" {$right = "User-Force-Change-Password"}
# "45ec5156-db7e-47bb-b53f-dbeb2d03c40"  {$right = "Reanimate-Tombstones"}
# "bf9679c0-0de6-11d0-a285-00aa003049e2" {$right = "Self-Membership"}
# "ba33815a-4f93-4c76-87f3-57574bff8109" {$right = "Manage-SID-History"}
# "1131f6ad-9c07-11d1-f79f-00c04fc2dcd2" {$right = "DS-Replication-Get-Changes-All"}

# ACL abuse scenarios
# https://sensepost.com/blog/2020/ace-to-rce/
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces
# https://adsecurity.org/?p=3658


# genericall-on-user1
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#genericall-on-user

SetAcl (Get-ADUser "n.collins") (Get-ADUser "a.adams") "GenericAll" "None"

# genericall-on-group
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#genericall-on-group

SetAcl (Get-ADUser "o.davidson") (Get-ADGroup "Domain Admins") "GenericAll" "None"

# genericall-genericwrite-write-on-computer
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#genericall-genericwrite-write-on-computer

SetAcl (Get-ADUser "g.white") (Get-ADComputer "W7-4$")  "WriteProperty" "All"

# writeproperty-on-group
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#writeproperty-on-group

SetAcl (Get-ADUser "q.kennedy") (Get-ADGroup "Domain Admins") "WriteProperty" "All"

# self-self-membership-on-group
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#self-self-membership-on-group

SetAclExtended (Get-ADUser "u.roberts") (Get-ADGroup "Domain Admins") "Self" "bf9679c0-0de6-11d0-a285-00aa003049e2" "None"

# writeproperty-self-membership
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#writeproperty-self-membership

SetAclExtended (Get-ADUser "f.west") (Get-ADGroup "Domain Admins") "WriteProperty" "bf9679c0-0de6-11d0-a285-00aa003049e2" "All"

# forcechangepassword
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#forcechangepassword
# https://docs.microsoft.com/fr-fr/windows/win32/adschema/r-user-change-password

SetAclExtended (Get-ADUser "l.james") (Get-ADUser "y.fox") "ExtendedRight" "00299570-246d-11d0-a768-00aa006e0529" "None"

# write owner on group
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#writeowner-on-group

SetAcl (Get-ADUser "a.graham") (Get-ADGroup "Domain Admins") "WriteOwner" "None"

# genericwrite-on-user
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#genericwrite-on-user

SetAcl (Get-ADUser "c.nelson") (Get-ADUser "w.marshall") "GenericWrite" "None"

# writedacl-writeowner
# https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces#writedacl-writeowner

SetAcl (Get-ADUser "p.kelly") (Get-ADGroup "RDP") "WriteDacl" "None"

exit
```


