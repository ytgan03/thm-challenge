# Creative

[https://tryhackme.com/room/creative](https://tryhackme.com/room/creative)

## Initial Search and Reconaissance 
The Nmap scan is performed as the first step to identify open ports. Based on the screenshot, `port 22` and `port 80` are opened on this machine.

By looking at the traceroute, we can determine the domain name associated with the IP address, which is 'creative.thm'.

Next, configure the `/etc/hosts` file by adding the machine’s IP address and domain name so that we can access the website.

Now, navigate to `http://creative.thm/`, and you will see the organization's website. However, after exploring the entire site, there is no meaningful information that can be exploited.

I attempted to use Gobuster to discover other directories on the website. `/assets` is one of the directories found. However, when I try to access it, the website displays a `Forbidden` message.

