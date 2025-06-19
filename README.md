# Intro
This is just a personal cheatsheet for OSCP study/HackTheBox/whatever & is primarily designed as such, mostly just listing out things I typically tend to forget. If it's helpful to anyone else at all, then awesome! This document is extremely a work in progress and I'll mostly be adding to it as I learn/remember things to add.

## Enumeration
### Web:
Always remember to check all of these!
- Subdomains: `ffuf -u http://<ip> -H "Host: FUZZ.domain.htb" -w /usr/share/seclists/Discovery/DNS/combined_subdomains.txt -ac`
  - -ac usually filters out the false positive responses, but if they slip through somehow you may have to try filtering by word, line count, regex, etc
- Subdirectories with BOTH ffuf and feroxbuster: (since feroxbuster works combinatorially testing it with combined_directories or any other very long wordlist is inadvisable, so it's best to use a shorter wordlist for it and a longer one for ffuf).
  - `ffuf -u http://domain.htb/FUZZ -w /usr/share/seclists/Discovery/Web-Content/combined_directories.txt -ac`
  - `feroxbuster -u 'http://domain.htb' -w '/usr/share/seclists/Discovery/Web-Content/raft-large-directories-lowercase.txt'`

### SMB:
`smbmap -H domain <flags>`
`nxc smb domain <flags>`
- When starting without creds, always try both without any creds at all and also these bogus cred combinations:
  - -u '' -p ''
  - -u 'test' -p ''
  - -u 'test' -p 'test'
  - -u '' -p 'test'
- netexec's SMB module is quite handy for password spraying, etc. See [the netexec wiki](https://www.netexec.wiki/) for further reference.
- with smbmap, use -r to recursively list shares and --depth to specify recursion depth. Usually 5 is sufficient to uncover anything
###LDAP:
- #1 priority should be collecting LDAP info for Bloodhound - there's minute differences on how each collector behaves, but I've generally found that SharpHound > Rusthound > nxc. Sharphound can typically only be used on the remote machine though, so for collecting info remotely Rusthound is the preferred option. I know some folks use bloodhound-python but I haven't had much success with it. Sharphound can pick up on some info that the remote collectors can't so as soon as you get a foothold, make sure to deploy it and re-scan!
- User enumeration:
  - It's pretty common to not be able to connect to LDAP without a proper bind. If you have a password you want to try spraying but don't know any usernames, Kerbrute can be a useful option:
  - `./kerbrute userenum -d domain.htb --dc <ip> -t 100 -o kerbrute.log /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt`
  - If you have any files, check the exif data! It can give you the general username format as well as a valid username to try against.
  - If you have access to SMB, try RID cycling to fetch usernames!
