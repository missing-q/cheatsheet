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
