Disclaimer: I did not wrote this cheat sheet, I just translated to english.
Source: https://github.com/lfgrillo83/Cyb3rW1k1/blob/master/enum

# Scanning and enumeration

`fping -g 10.0.131.0/24 (search for active IPs in network)`

`nmap -sn 10.0.131.0/24 (Do not make a port scan)`

`hping3 --syn -c 1 -p 80 www.businesscorp.com` (perform a network test on port 80 (similar to ping))

# Bypassing firewall
`nmap -n -v -sS -g 53 www.businesscorp.com` (uses port 53 as origin)

`nc -p53 -nv 192.168.10.102 8180` (uses port 53 as origin) -> also recommended using source as 80 or 443 !!!

# HTTP(S) enumeration
## HTTP
`nc -v www.businesscorp.com <port>`

`GET / HTTP/1.0`

`Host: www.businesscorp.com`

[ENTER]

## HTTPS
`openssl s_client -quiet -connect www.businesscorp.com:443`

`HEAD / HTTP/1.0`

`Host: www.businesscorp.com`

[ENTER]

# SMB Enumeration
## Linux
`nmap -n -v -Pn -sV --open -p 139,445 -oA smb 192.168.10.0/24`

`ls /usr/share/nmap/s/*smb*`

`nmap -v --script=smb-os-discovery 192.168.10.102`

`nmap -v --script=smb-enum-shares 192.168.10.102`

`nbtscan -r 192.168.10.0/24` # Search the network for hosts with smb

`smbclient -L //172.16.1.5 -N` # list without password

`smbclient -L //172.16.1.5 -N -U administrator` # list administrator without password

`smbclient //192.168.10.102/tmp -N` # connect to the mount point
- smb: \> `ls` #directory listing
- smb> `get data.txt`
- smb> `put evil.txt`
- smb: \> `exit`

`rpcclient -U "" -N 192.168.10.102`
- rpcclient $> `?` #show commands
- rpcclient $> `enumdomusers`
- rpcclient $> `netshareenum`
- rpcclient $> `querydominfo`
- rpcclient $> `lookupnames root`

`rpcclient -U "administrador" -N 192.168.10.101`

`enum4linux -a 192.168.10.102`

`enum4linux -a  -u administrador -p "" 192.168.10.102` # administrator without password


### Brute SMB password:
`nmap -p445 --script=smb-brute.nse <ip>` # Brute force should always be your last option. You can also use hydra to do it.

`nmap -sU -sS --script=smb-enum-users -p U:137,T:139 192.168.1.200-254`

`nmap -T4 -v -oA shares -- smb-enum-shares ---args smbuser=username,smbpass=password -p445 192.168.1.0/24`

### Windows null session:
C:\> `net use \\TARGET\IPC$ "" /u:""`

### Use acccheck for getting user pass using smb
`acccheck -v -t 192.168.1.2 -u <user_name> -P /usr/share/dirb/wordlist/common.txt`

`acccheck -t 192.168.1.2 -U /root/users.txt -P /root/Pass.txt`

Once you got user creds we will use the creds to see the shares using smbmap

`smbmap -u <user_name> -p <password> -d <domain> -H <IP>`

`smbmap -u user -p pass -d workgroup -H 192.168.1.2`

`smbmap -L -u user -p pass -d workgroup -H 192.168.1.2`

If you have only read privilege read the shares

`smbmap -r -u user -p pass -d workgroup -H 192.168.1.2`

## Windows

`nbtstat -A 192.168.10.102` # Show information about the target

`net use \\192.168.10.102 "" /u:""` # mount drive with null user (if accepted)

`net view \\192.168.10.102` # show shares

`net use x: \\192.168.10.102\temp` # mount share temp on disk x:

`net use` # Show active/mounted shares

`net use \\192.168.10.102 "" /u:administrator` # try to access with administrator without password

`net use \\192.168.10.102 /delete` # Disconnect

`net use x: /delete` # Unmount

# SMTP enumeration

```shell
nc -vn 192.168.10.102 25
VRFY root
VRFY foo
VRFY admin
# You can enumerate users with this
```

# SNMP enumeration

Main goal is to extract data
Default communities: public, private, manager

`nmap -v --open -sUV -p161 -Pn 192.168.10.102` #(-sUV resolves filtered ports's problem)

Create a file with communities to use (eg: communities.txt)
```shell
for ip in $(seq 1 254); 
do echo 192.168.10.$ip; 
done >> ips
```
`onesixtyone -c comunities.txt -i hosts.txt`

`snmpwalk -c public -v1 192.168.10.102 1.3.6.1.4.1.77.1.2.25` #(example mib for users in windows)

`snmp-check -t 192.168.10.102` # this script makes automated tests and output the results in a human readable way

# Default passwords

www.defaultpassword.com

www.routerpasswords.com

# MySQL enumeration

`nmap -n -v -Pn --open -sS -p 3306 192.168.10.0/24`

`mysql -h 192.168.10.102 -u root`

# DNS enumeration

`dig @189.20.90.19 version.bind chaos txt` # Swap dig for drill on ArchLinux

`fpdns -D site.com.br`

address that has periodicall reports of versions, top 100 hosts etc...:
https://ftp.isc.org/www/survey/reports/current/

# nmap default scan

`nmap -n -v -Pn -sS -sV -A IP_REDE_DEST --reason --traceroute`

# Eternal blue (MS17-010) check
`nmap --script=smb-vuln-ms17-010,smb-double-pulsar-backdoor,smb-vuln-cve-2017-7494 ---args vulns.short 192.168.225.0/24 -p 135,445 --open -oA MS17010`