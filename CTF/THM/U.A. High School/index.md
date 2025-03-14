# U.A. High School

[https://tryhackme.com/room/yueiua](https://tryhackme.com/room/yueiua)

## Recon & Enumeration 
To compromise this room, I first performed an Nmap scan to identify open ports. The scan revealed two open ports: `80 and 22`. I then accessed the website hosted on port 80 but found no meaningful information. 

![Nmap scan](images/image-1.png)

To uncover hidden directories, I used Gobuster with the common.txt wordlist from SecLists. This scan revealed a directory: `/assets/index.php`, which returned a 200 status code. 

![Directory Enumeration](images/image-2.png)
![Directory Enumeration](images/image-3.png)

However, when I accessed the URL `http://MACHINE_IP/assets/index.php`, it displayed a blank page. Suspecting that this index.php file might be exploitable, I tested common web shell commands. 

`<?php echo passthru($_GET['cmd']); ?>`

`<?php echo exec($_POST['cmd']); ?>`

`<?php system($_GET['cmd']); ?>`

`<?php passthru($_REQUEST['cmd']); ?>`

To verify this, I appended a command to the URL:
`http://MACHINE_IP/assets/index.php?cmd=ls`

This returned a random string, which I identified as Base64-encoded output. 

![Base64-encoded output](images/image-4.png)

To decode it, I used:
```
curl http://MACHINE_IP/assets/index.php?cmd=ls | base64 -d
```
![Command Execution](images/image-5.png)

## Gaining a Reverse Shell
With command execution confirmed. I proceeded to gain a reverse shell:
1. Generated a reverse shell payload using `revshells.com`<br>
    ![Reverse shell payload](images/image-6.png)
2. Set up a Netcat listener: `nc -lvnp 4444`<br>
    ![Netcat listener](images/image-7.png)
3. Once connected, I upgraded the shell for stability:<br>
    ![Stabilise the shell](images/image-9.png)
4. Executed the reverse shell by appending the payload to the vulnerable URL.
    ![Append the payload](images/image-8.png)

## Privilege Escalation
At this point, I had access as the www-data user. To escalate privileges:
1. Explored directories and found a corrupt JPG file.<br>
    ![Corrupted JPG file](images/image-10.png)
2. Downloaded the file using `wget` and identify the file type using `MagicBytes`. 
    ![File downloads and identification](images/image-11.png)
3. The file revealed an image of Deku which hinted at Steganography.
    ![Image reveal](images/image-12.png)
4. Use `Steghide` to check for hidden data within the image, but it required a password. 
    ![Hidden data decryption](images/image-13.png)
5. Searched directories thoroughly and found `Hidden_Content/passphrase.txt`, which contained a Base64-encoded password.
    ![Credentials harvesting](images/image-14.png)
    ![Credentials harvesting](images/image-15.png)
6. Decoded the password and used it to extract a hidden text file from the image, revealing Deku’s credentials. 
    ![Password decryption](images/image-16.png)
    ![Credentials disclosure](images/image-17.png)

## Root Privilege Escalation
With Deku’s credentials, I logged in and found `user.txt`.

![Retrieved user's flag](images/image-18.png)
![User's flag](images/image-19.png)

Before escalating to root, I checked sudo privileges using `sudo -l`.

![Sudo Privilege Enumeration](images/image-20.png)

This revealed that `/path/feedback.sh` could be executed as root. After analyzing the script, I found it vulnerable to arbitrary command execution. 

![Vulnerability found](images/image-21.png)
![Script exploitation](images/image-22.png)

To gain root access quickly, I exploited the script to add Deku to the sudoers file using `deku ALL=NOPASSWD: ALL >> /etc/sudoers`.

![Vulnerability exploitation](images/image-23.png)

We can check our sudo privileges again using `sudo -l`. As we can see from the screenshot, sudo privileges are now open to every user. 

![Privilege escalation](images/image-24.png)

Now, with root privileges, I accessed the final flag using `cat /root/root.txt`. 

![Retrieved root's flag](images/image-25.png)
