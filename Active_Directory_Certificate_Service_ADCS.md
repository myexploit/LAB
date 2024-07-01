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









