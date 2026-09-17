# Cybersecurity Fundamentals: 50 Beginner MCQs

**Audience:** First-year college students  
**Level:** Beginner  
**Format:** 50 questions, four options each, one correct answer  
**Focus:** Security tools, assessment methods, networking concepts, and system security

Choose the best answer to each question. All questions are theoretical and self-contained. No lab access, practical tasks, or command execution is required. The correct answer appears below each question.

## Network Discovery and Enumeration

### 1. Which tool is commonly used to discover open network ports?

- **A.** Wireshark
- **B.** Nmap
- **C.** John the Ripper
- **D.** LinPEAS

**Answer: B. Nmap**

### 2. A scan reports an open TCP port. What does this usually mean?

- **A.** The administrator password has been recovered
- **B.** The entire firewall has been disabled
- **C.** The service has a confirmed security vulnerability
- **D.** A service is accepting connections on that port

**Answer: D. A service is accepting connections on that port**

### 3. Why identify the version of a running service?

- **A.** To prove that every installation is vulnerable
- **B.** To remove the service from the network
- **C.** To obtain its administrator password automatically
- **D.** To check whether known weaknesses may affect it

**Answer: D. To check whether known weaknesses may affect it**

### 4. What does enumeration mean in a security assessment?

- **A.** Encrypting all files stored on a computer
- **B.** Installing updates on every network device
- **C.** Deleting records of previous network activity
- **D.** Collecting detailed information about services and accounts

**Answer: D. Collecting detailed information about services and accounts**

### 5. Which tool can test a list of possible website directory names?

- **A.** Wireshark
- **B.** Netcat
- **C.** Gobuster
- **D.** John the Ripper

**Answer: C. Gobuster**

### 6. An administrator removes a link to a sensitive webpage. Why might it still be accessible?

- **A.** Web browsers automatically disable authentication on unlinked pages
- **B.** Every unlinked page becomes a public network share
- **C.** Someone may reach it by knowing or guessing its URL
- **D.** Removing a link automatically grants administrator access

**Answer: C. Someone may reach it by knowing or guessing its URL**

### 7. What is Enum4linux commonly used to collect?

- **A.** Information about SMB or Samba users and shares
- **B.** A list of possible website directory names
- **C.** Passwords from encrypted SSH private keys
- **D.** The contents of captured network packets

**Answer: A. Information about SMB or Samba users and shares**

### 8. What is a common purpose of SMB?

- **A.** Synchronising clocks between network devices
- **B.** Sending outgoing email between mail servers
- **C.** Sharing files and printers over a network
- **D.** Resolving website names into IP addresses

**Answer: C. Sharing files and printers over a network**

### 9. A service version resembles one listed in a vulnerability report. What is the best conclusion?

- **A.** All exploits for that product will work
- **B.** Its configuration and patch status still need checking
- **C.** The server is definitely fully compromised
- **D.** Every account password can now be recovered

**Answer: B. Its configuration and patch status still need checking**

### 10. Which activity normally comes before selecting an exploit?

- **A.** Collecting administrator password hashes
- **B.** Changing the privileges of an existing session
- **C.** Searching protected folders through a remote shell
- **D.** Identifying the target service and relevant weaknesses

**Answer: D. Identifying the target service and relevant weaknesses**

## Network Traffic and Protocols

### 11. Which tool provides a graphical interface for examining network packets?

- **A.** Gobuster
- **B.** Searchsploit
- **C.** Hydra
- **D.** Wireshark

**Answer: D. Wireshark**

### 12. What does a PCAP file usually contain?

- **A.** A backup of user account settings
- **B.** Recorded network packets
- **C.** A list of installed applications
- **D.** A collection of SSH private keys

**Answer: B. Recorded network packets**

### 13. What does Follow TCP Stream help an analyst examine?

- **A.** Captured data exchanged within a TCP connection
- **B.** Every password stored on the target computer
- **C.** Future traffic before it reaches the network
- **D.** All files deleted from the target disk

**Answer: A. Captured data exchanged within a TCP connection**

### 14. Why apply a display filter in Wireshark?

- **A.** To repair the vulnerable service automatically
- **B.** To encrypt packets that were already captured
- **C.** To block a remote account from logging in
- **D.** To show packets relevant to the investigation

**Answer: D. To show packets relevant to the investigation**

### 15. Why can ordinary FTP expose login credentials on a network?

- **A.** It requires every login to use a private key
- **B.** It replaces each password with a secure hash
- **C.** It sends them without encryption by default
- **D.** It stores all credentials inside a certificate

**Answer: C. It sends them without encryption by default**

### 16. What is a key security benefit of SSH for remote login?

- **A.** It gives every user administrator access
- **B.** It encrypts communication between client and server
- **C.** It guarantees that every password is strong
- **D.** It prevents all software vulnerabilities

**Answer: B. It encrypts communication between client and server**

### 17. Many failed logins to one account appear within a short time. What might this indicate?

- **A.** A routine network service discovery scan
- **B.** A completed disk-encryption operation
- **C.** An attempt to guess the account password
- **D.** A successful security update

**Answer: C. An attempt to guess the account password**

### 18. In a captured packet, what does the destination IP address identify?

- **A.** The account that last changed the password
- **B.** The network address the packet is being sent to
- **C.** The directory where the packet is stored
- **D.** The software version of the sending application

**Answer: B. The network address the packet is being sent to**

### 19. Which feature is associated with TCP?

- **A.** Automatic encryption of every connection
- **B.** Reliable, ordered delivery of a stream of bytes
- **C.** Conversion of domain names into IP addresses
- **D.** Assignment of administrator rights to users

**Answer: B. Reliable, ordered delivery of a stream of bytes**

### 20. Why examine captured traffic after a suspected attack?

- **A.** To automatically reverse all system changes
- **B.** To prove that no uncaptured activity occurred
- **C.** To reconstruct activity visible in the capture
- **D.** To guarantee recovery of every deleted file

**Answer: C. To reconstruct activity visible in the capture**

## Passwords, Hashes, and SSH Keys

### 21. What is the basic idea of password brute forcing?

- **A.** Trying many candidate passwords to find a match
- **B.** Encrypting a password before saving it
- **C.** Inspecting which network ports are open
- **D.** Updating a service to its latest release

**Answer: A. Trying many candidate passwords to find a match**

### 22. How does a dictionary attack choose password guesses?

- **A.** It chooses guesses from open port numbers only
- **B.** It retrieves the password directly from DNS
- **C.** It uses entries from a prepared wordlist
- **D.** It asks the operating system to reveal the password

**Answer: C. It uses entries from a prepared wordlist**

### 23. Which tool is commonly used to test password guesses against network logins?

- **A.** Hydra
- **B.** LinPEAS
- **C.** Gobuster
- **D.** Wireshark

**Answer: A. Hydra**

### 24. Which tool is commonly used to test password guesses against captured hashes?

- **A.** John the Ripper
- **B.** Enum4linux
- **C.** Netcat
- **D.** Nmap

**Answer: A. John the Ripper**

### 25. Which statement best describes a password hash?

- **A.** A network address assigned to a user account
- **B.** A one-way value used to check a password
- **C.** An encrypted password designed for direct decryption
- **D.** A public key used for SSH authentication

**Answer: B. A one-way value used to check a password**

### 26. What distinguishes offline password cracking from online login guessing?

- **A.** Offline cracking always reveals the password immediately
- **B.** Offline cracking tests captured data without contacting the login service
- **C.** Offline cracking works only when no password exists
- **D.** Offline cracking requires sending every guess to the login service

**Answer: B. Offline cracking tests captured data without contacting the login service**

### 27. Which part of an SSH key pair must the owner keep secret?

- **A.** The key type name
- **B.** The private key
- **C.** The public key
- **D.** The public-key fingerprint

**Answer: B. The private key**

### 28. What is the purpose of a passphrase on an SSH private key?

- **A.** To make the public key unnecessary on the server
- **B.** To change the IP address of the SSH server
- **C.** To help protect the stored key if someone copies it
- **D.** To allow every user to read the private key

**Answer: C. To help protect the stored key if someone copies it**

### 29. Why can an exposed backup file create a security risk?

- **A.** Its filename guarantees that it contains malware
- **B.** It automatically disables encryption on the server
- **C.** It always gives the reader root privileges
- **D.** It may contain credentials or other sensitive information

**Answer: D. It may contain credentials or other sensitive information**

### 30. Which file-access setting best protects a private SSH key?

- **A.** Every local user can read and modify it
- **B.** Every website visitor can download it
- **C.** Anonymous users can replace it through FTP
- **D.** Only its owner can read and modify it

**Answer: D. Only its owner can read and modify it**

## Vulnerabilities, Exploits, and Remote Access

### 31. What is the difference between a vulnerability and an exploit?

- **A.** A vulnerability is a weakness; an exploit takes advantage of it
- **B.** A vulnerability is a log; an exploit stores that log
- **C.** A vulnerability is a password; an exploit encrypts it
- **D.** A vulnerability is a fix; an exploit installs that fix

**Answer: A. A vulnerability is a weakness; an exploit takes advantage of it**

### 32. What is Metasploit commonly used for in an authorised assessment?

- **A.** Testing vulnerabilities using exploit and supporting modules
- **B.** Examining captured packets in a graphical interface
- **C.** Testing directory names against a website
- **D.** Cracking captured password hashes with a wordlist

**Answer: A. Testing vulnerabilities using exploit and supporting modules**

### 33. In exploitation, what is a payload?

- **A.** Code that performs an action after a weakness is exploited
- **B.** A document listing the agreed assessment scope
- **C.** An update that repairs the vulnerable software
- **D.** A filter that hides unrelated packets from view

**Answer: A. Code that performs an action after a weakness is exploited**

### 34. What does remote code execution mean?

- **A.** Viewing a public webpage in a browser
- **B.** Running code on another system through a vulnerability
- **C.** Copying a report to an approved shared folder
- **D.** Reading a software manual from another computer

**Answer: B. Running code on another system through a vulnerability**

### 35. In a reverse shell, which side starts the network connection?

- **A.** A DNS server starts a connection to both systems
- **B.** No network connection is required
- **C.** The target system connects back to a listening system
- **D.** The listening system always connects to the target first

**Answer: C. The target system connects back to a listening system**

### 36. What does a network listener do?

- **A.** Waits for an incoming connection on a chosen port
- **B.** Guesses passwords from a prepared wordlist
- **C.** Checks whether a file contains an SSH key
- **D.** Searches locally stored exploit descriptions

**Answer: A. Waits for an incoming connection on a chosen port**

### 37. When configuring a reverse connection in Metasploit, what does LHOST usually specify?

- **A.** The address the target should connect back to
- **B.** The location of the password wordlist
- **C.** The username of the remote administrator
- **D.** The version of the vulnerable application

**Answer: A. The address the target should connect back to**

### 38. What does RHOSTS usually identify in a Metasploit module?

- **A.** The current user permissions
- **B.** The local report directory
- **C.** The target host or hosts
- **D.** The available password hashes

**Answer: C. The target host or hosts**

### 39. What is the main purpose of Searchsploit?

- **A.** Searching a local copy of Exploit-DB entries
- **B.** Discovering hidden website directories
- **C.** Guessing passwords against SSH logins
- **D.** Capturing live network packets

**Answer: A. Searching a local copy of Exploit-DB entries**

### 40. What is the purpose of a CVE identifier?

- **A.** To guarantee that an exploit works on every system
- **B.** To reveal the administrator password of a server
- **C.** To identify the owner of every IP address
- **D.** To provide a shared reference for a disclosed vulnerability

**Answer: D. To provide a shared reference for a disclosed vulnerability**

## Privileges, System Checks, and Prevention

### 41. What is privilege escalation?

- **A.** Listing files already accessible to the current user
- **B.** Signing in again with the same permissions
- **C.** Changing the password of your own account
- **D.** Obtaining permissions beyond those initially available

**Answer: D. Obtaining permissions beyond those initially available**

### 42. What information does the Linux command whoami display?

- **A.** The username associated with the current effective user ID
- **B.** The list of open network ports
- **C.** The password of the current user
- **D.** The version of the web server

**Answer: A. The username associated with the current effective user ID**

### 43. Which Linux account traditionally has full administrative privileges?

- **A.** guest
- **B.** root
- **C.** nobody
- **D.** www-data

**Answer: B. root**

### 44. What does sudo -l help a user check?

- **A.** Which websites were visited most recently
- **B.** Which commands they are permitted to run through sudo
- **C.** Which passwords appear in a wordlist
- **D.** Which network ports are accepting connections

**Answer: B. Which commands they are permitted to run through sudo**

### 45. What is LinPEAS designed to help identify?

- **A.** Hidden directories on a remote website
- **B.** Passwords from a captured FTP conversation
- **C.** Linux weaknesses that may support privilege escalation
- **D.** IP addresses returned by a DNS query

**Answer: C. Linux weaknesses that may support privilege escalation**

### 46. What is Meterpreter?

- **A.** A Linux service that installs software updates
- **B.** A network protocol used to transfer email
- **C.** A file format used to store packet captures
- **D.** An advanced session environment provided by Metasploit

**Answer: D. An advanced session environment provided by Metasploit**

### 47. What does NT AUTHORITY\SYSTEM represent on Windows?

- **A.** A built-in account with extensive local system privileges
- **B.** A group containing only remote guest users
- **C.** An anonymous visitor to a public website
- **D.** A standard account created for every new student

**Answer: A. A built-in account with extensive local system privileges**

### 48. What is the purpose of Windows User Account Control, or UAC?

- **A.** To recover forgotten passwords from hashes
- **B.** To discover hidden folders on remote websites
- **C.** To control when applications receive elevated privileges
- **D.** To encrypt every network connection automatically

**Answer: C. To control when applications receive elevated privileges**

### 49. Why install a security update for a known software vulnerability?

- **A.** To remove the need for access controls
- **B.** To guarantee protection against every future attack
- **C.** To make all users local administrators
- **D.** To apply the vendor fix for the affected weakness

**Answer: D. To apply the vendor fix for the affected weakness**

### 50. What does the principle of least privilege recommend?

- **A.** Allow every user to read all private keys
- **B.** Give users and services only the permissions they need
- **C.** Use one shared privileged account for all activities
- **D.** Give every account administrator rights for convenience

**Answer: B. Give users and services only the permissions they need**

