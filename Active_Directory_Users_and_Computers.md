Living-off-the-land (LOTL) technique to access basic Active Directory Users and Computers.

While this is an interesting technique, it’s not that useful as it only appears to reveal one domain group per username, but it does allow you to enumerate all machine names, usernames within Active Directory.

The testing accounts domain group membership.

```
User name                    g.white
Full Name
Comment
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            21/11/2024 20:56:33
Password expires             02/01/2025 20:56:33
Password changeable          22/11/2024 20:56:33
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   10/12/2024 21:01:20

Logon hours allowed          All

Local Group Memberships
Global Group memberships     *Domain Users
The command completed successfully.

```

1. Right click on any folder and select Properties.

![image](https://github.com/user-attachments/assets/1fbc8cf5-f6e7-4cac-93ec-bfb4b2623c07)

2. Click on the Security tab followed by Advanced.

![image](https://github.com/user-attachments/assets/09764bfd-f177-4957-8cfc-6d7792ecbdff)

3. Click on Add.

![image](https://github.com/user-attachments/assets/fc6d1d2b-7f88-458e-89c1-83ae664adeef)

4. Click on Select a principal.

![image](https://github.com/user-attachments/assets/f6437fa7-adae-4a11-a632-1ea07bfbe2b1)

5. This loads the basic Active Directory Users and Computers search menu.

![image](https://github.com/user-attachments/assets/3b188497-58f9-4ed1-90ff-0ca316cdb975)

6. If you click on locations you can see the basic OU's.

![image](https://github.com/user-attachments/assets/876ddfa6-4428-451a-b3bf-1601163d7241)

7. Click on Advanced, which opens up a basic search feature, which enables slightly more granular controller.

![image](https://github.com/user-attachments/assets/38a86bff-33cc-42a3-8442-28291c228395)

8. Click on Columns, highlight “Member Of”, Click Add, Click OK.

![image](https://github.com/user-attachments/assets/86dac1b5-abdd-4c8c-a803-404a2e7562ec)

9. Type in an accounts name you wish to enumerate and click Find Now.

![image](https://github.com/user-attachments/assets/e476c061-a03d-41fa-862a-523357aa0df8)

You can only see the first domain group the account is associated with from this limited search function, but it’s still a useful technique for stealthy enumeration.

![image](https://github.com/user-attachments/assets/5ba938c4-31e5-46fa-8f3a-9b25945492ba)
