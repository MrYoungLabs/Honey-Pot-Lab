# Honey Pot Lab

Personal notes on building a simple network honeypot, based on a hands-on "Setting Up a Honeypot" exercise from an ethical hacking training course. Rewritten in my own words as a general-purpose reference for rebuilding something similar on my own home/SOHO network.

## What is a honeypot?

A honeypot is a decoy service or host deliberately exposed so that anyone probing or attacking it interacts with a fake target instead of a real one. Since nothing legitimate should ever talk to it, any connection attempt is a strong signal of scanning or intrusion activity, and the honeypot can log the source and nature of that attempt for later review.

## Goals of this lab

Identify the IP address of the machine that will host the honeypot, then stand up a basic honeypot listener on a chosen port and confirm it detects and logs a connection attempt.

## Tools used

A Linux host or VM was used for the honeypot itself (the lab used a Kali-based image). Pentbox, a Ruby-based toolkit that includes a simple manual honeypot module, provides the actual listener. A browser is used from another device to generate a test connection against the honeypot.

## Part A: Find the host's IP address

Open a terminal on the Linux host and run ifconfig (on newer distros, ip a is the modern equivalent) to list network interfaces. Note the inet address of the active interface, such as eth0 or ens33 - this is the address the honeypot will listen on, and the address test traffic will be pointed at later.

## Part B: Configure and run the honeypot

From the terminal, change into the Pentbox directory and launch it with cd pentbox-1.8/ then ./pentbox.rb. From the main menu, choose option 2 for Network tools, then option 3 for Honeypot, then option 2 for Manual configuration (Pentbox also offers a "fast" preset, but manual configuration lets you pick the exact port). Enter the port you want the honeypot to watch - the lab used 443 (HTTPS) as an example, but for a SOHO setup you would instead pick a port that mirrors a service you want to protect or bait, such as 22 for SSH, 23 for Telnet, or 3389 for RDP. Provide a decoy banner or message that will be shown to whoever connects (the lab used a simple string like "Caught You!!"), confirm that you want to save a log file and accept the default log location or set your own, then decline the audible beep-on-connection option unless you specifically want it. Leave the honeypot running in that terminal window.

## Part C: Trigger and verify detection

From another device or a browser on the same network, connect to the honeypot's address and port (in the lab this was `https://<ip address>` on port 443, substituting the inet address found in Part A). The connecting client should fail to get a real response, such as a "secure connection failed" style error in the browser, because there is no real service behind that port. Switch back to the honeypot terminal - it should print an intrusion-attempt alert (the lab's Pentbox build showed a banner like "INTRUSION ATTEMPT DETECTED!") along with the connecting IP, confirming the honeypot logged the probe. Press Ctrl+C to stop the listener when finished testing, and close out the terminal.

## What this demonstrates

This exercise is good practice for identifying the correct network interface and IP to bind a service to, seeing a minimal working example of a decoy service that logs unsolicited connections, and getting a first-hand look at what an intrusion-attempt log entry looks like before moving on to more advanced tools.

## Adapting this for a real SOHO honeypot

A few important differences exist between a classroom lab and a real home or office deployment. Isolate the honeypot: don't run it on a machine that also holds personal data or production services - ideally put it on its own VLAN, a guest network, or a dedicated low-power device such as an old laptop or Raspberry Pi. Control exposure carefully by only forwarding the specific bait port(s) from the router to the honeypot host, rather than placing the whole machine in a DMZ. Pick believable bait by choosing ports and services that resemble what a real attacker would expect to find, such as SSH, RDP, or a web login page, rather than arbitrary ports. Review logs regularly, since a honeypot is only useful if someone looks at what it caught; consider forwarding its logs to a central place such as a syslog server or a simple monitoring script. Stay within legal and ethical boundaries: logging connection attempts to your own honeypot is fine, but scanning back at or attempting to access systems that connected to it is not. Finally, consider purpose-built tools for anything beyond learning - Pentbox's honeypot module is intentionally simple, and tools such as Cowrie (an SSH/Telnet honeypot), Honeyd, or the T-Pot platform are better suited to real-world, longer-term deployments.

## Source

This documentation is a rewritten summary of concepts covered in a "Setting Up a Honeypot" lab from an Ethical Hacking training course (uCertify platform). It intentionally avoids copying the course's exact text, screenshots, or figures, since those are proprietary course materials.
