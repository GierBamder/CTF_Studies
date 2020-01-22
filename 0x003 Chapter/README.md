# Chapter 0x003

## Enumeration Concepts

Enumeration is an attack phase where and performs directed queries to gain more information about the target. This phase usually happen inside an internal network, but can also happen over the Internet. They use the information to identify possible attack vectors on systems and try password attacks to get access to those systems. 

## Enumeration techniques

- A good way to get usernames is getting the e-mail of a victim.
- Many times, people use default passwords for systems.
- Brute force the administrator of a domain, it cannot be locked out, but be carefull with dettection.
- DNS Zones can give you some cool information.
- Windows's user groups can be useful.
- If you found SNMP traffic which is v2 or v1, you found the golden mine.
- Null session - An account who has no name and no password, but can connect on microsoft systems.
    - `net use \\<host>\ipc$ "" /u:""` - Connect to a remote host using null user
    - `net view \\<host>` - Show content from connected host
- SNMP has a default of `public` to the read-only community, and `private` to the read/write community, try them. It runs on port 161. OIDS:
    - Windows running processes - 1.3.6.1.2.1.25.4.2.1.2
    - Windows installed software - 1.3.6.1.2.1.25.6.3.1.2
    - Windows system info - 1.3.6.1.2.1.1.1
    - Windows hostname - 1.3.6.1.2.1.1.5
    - Windows domain - 1.3.6.1.4.1.77.1.4.1
    - Windows uptime - 1.3.6.1.2.1.1.3
    - Windows Users - 1.3.6.1.4.1.77.1.2.25
    - Windows shares - 1.3.6.1.4.1.77.1.2.27
    - Windows disks - 1.3.6.1.2.1.25.2.3.1.3
    - Windows services - 1.3.6.1.4.1.77.1.2.3.1.1
    - Windows Listening tcp ports - 1.3.6.1.2.1.6.13.1.3.0.0.0.0
    - Windows Listening udp ports - 1.3.6.1.2.1.7.5.1.2.0.0.0.0
- If you have access to an account, LDAP can be used to enumerate user accounts in a domain
- SMTP can be used to validade users and show mail aliases and lists. Usually needs authentication, not **always** the case.

## Enumeration - Tools

- nbtstat - Windows native
    - `nbtstat.exe -a <remoteIP>` gets you the NetBIOS name table of the remote machine and the MAC Address - can also be obtained with nmap `nmap -sU --script nbstat.nse -p137 <host>`
- PsTools - Windows tool to get many windows internal information.
    - `psgetsid` - Get the SID of the system. User SID is made of systemSID + -relativeAccountID
    - `psgetSID <systemID>-500` - Resolves to the native account who has administrator privileges, even if the Administrator account has been renamed



## Enumerating Windows


## Enumerating Linux


## Tips for enumeration
