# OverTheWire: Bandit

Bandit is an OverTheWire lab that focuses on the shell.

I used a Kali Linux VM to solve all the challenges.

## Level 0

I connected to the lab using this command:

```$ ssh bandit0@bandit.labs.overthewire.org -p 2220``` 

ssh is used for remote connections. Here I'm connecting to bandit.labs.overthewire.org using port 2220 and with username bandit0.

(picture)

According to the instructions, the password is bandit0.

After entering the password, we're ready to go to the next Level.

(picture)

## Level 0 -> Level 1

Next level's password is stored in a file called readme inside the home directory.

In order to visualize current directory's contents, we can use  ```ls ```:

(picture)

This displays the readme file. To see what's inside it, we can use  ```cat```, which is used, among other things, to display file contents:
```$ cat readme```

This outputs the password.

To access the next level, we first exit and then run the same command as we did at Level 0, but using bandit1 with the new password:
```bash
$ exit
$ ssh bandit1@bandit.labs.overthewire.org -p 2220
```

If we wish to empty the terminal, we can use the command ```clear```.

## Level 1 -> Level 2

To find the file located in "-":

We make sure it's in the current working directory.
```$ ls```

We visualize its contents, which in this case is the password.
```$ cat ./-```

In Linux, dashes (-) are used to specify options for commands. If we were to use the dash alone with ```cat```, the command would remain executing.

```./``` is the file's path, where ```.``` is the current directory, so here we're declaring that we're dealing with a file.

## Level 2 -> Level 3

Now the password is stored in a file called --spaces in this filename--.

Similar to the single dash, double hyphens are also used to specify options. Particularly, double hyphens are often used as the longer written form of the single dash one.

This time we also have to deal with spaces; typically the command line will take spaces as different arguments. In order to counter this, we surround the filename in quotes.
```$ cat ./"--spaces in this filename--"```

This gives us this level's password.

## Level 3 -> Level 4

First we visualize the current directory:
```$ ls```

Here, we have two choices: 

1. We go to the ```inhere``` directory using ```cd```:

```$ cd inhere```

In this level, the password is stored in a hidden file. To show its name, we use the ```-a``` (all) flag after ```ls```.

```$ ls -a```

There, we find a file named ```...Hiding-From-You```.

Finally, we display the contents using ```cat```:
```$ cat ...Hiding-From-You```

2. We can print the file's contents without changing directories using paths:

We visualize ```inhere```'s contents:
```$ ls -a inhere```

Then we output the desired file's contents:
```$ cat inhere/...Hiding-From-You```

## Level 4 -> Level 5

Next level's password is stored in the only human-readable file in inhere.

```bash
$ cd inhere
$ find . -type f -printf "\n\n" -exec cat {} \;
```

## Level 5 -> Level 6

Password is stored in the ```inhere``` directory, is human-readable, is 1033 bytes, and isn't executable

```$ find . -type f ! -executable -size 1033c -printf "\n\n" -exec cat {} \;```

## Level 6 -> Level 7

File is stored somewhere owned by user bandit7, owned by group bandit6, 33 bytes in size

```$ find / -type f -size 33c -group bandit6 -user bandit7 -printf "\n\n" -exec cat {} \;```


## Level 7 -> Level 8
Password is found in data.txt next to the word "millionth"

```$ grep data.txt -e "millionth"```

## Level 8 -> Level 9
The password is in data.txt and is the only unique line of text

```$ sort data.txt | uniq -u```

## Level 9 -> Level 10
Password is in data.txt, in a human-readable string, preceded by several "="

```$ strings data.txt | grep -e '.=='```

## Level 10 -> Level 11
Next level's password is stored in data.txt, which contains base64 data.

```$ base64 -d data.txt```

## Level 11 -> Level 12
The password is stored in data.txtm where lowercase and uppercase were rotated by 13 positions

```$ cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'```

## Level 12 -> Level 13
data.txt is now a hexdump that has been compressed several times. Creating a directory is useful

```$ xxd -r data.txt data.bin```
To turn the hexdump into binary

Analyze the file 
```$ file data.bin``` 
(picture)
```bash
$ mv data.bin data.gz
$ file data
```
Output: <mark>data: bzip2 compressed data, block size = 900k</mark>

```bash
$ mv data data.bz2
$ bunzip2 data.bz2
$ file data
```
Output: <mark>data: gzip compressed data, was "data4.bin", last modified: Wed Jun 24 14:58:46 2026, max compression, from Unix, original size modulo 2^32 20480</mark>

```bash
$ mv data data.gz
$ unzip data.gz
$ file data
```
Output: <mark>data: POSIX tar archive (GNU)</mark>

```bash
$ man tar
$ mv data data.tar
$ tar -xvf data.tar
$ file data5.bin
```
Output: <mark>data5.bin: POSIX tar archive (GNU)</mark>

```bash
$ tar -xvf data5.bin
$ file data6.bin
```
Output: <mark>data6.bin: bzip2 compressed data, block size = 900k</mark>

```bash
$ bunzip2 data6.bin
$ file data6.bin.out
```
Output: <mark>data6.bin.out: POSIX tar archive (GNU)</mark>

```bash
$ tar -xvf data6.bin.out
$ file data8.bin
```
Output: <mark>data8.bin: gzip compressed data, was "data9.bin", last modified: Wed Jun 24 14:58:46 2026, max compression, from Unix, original size modulo 2^32 49</mark>

```bash
$ gunzip data8.bin
$ mv data8.bin data8.gz
$ gunzip data8.gz
$ file data8
```
Output: <mark>data8: ASCII text</mark>

```bash
$ cat data8
```
Output: <mark>The password</mark>

## Level 13 -> Level 14
Next level's password is in /etc/bandit_pass/bandit14 and can only be read by user bandit14

There are only two files in the directory: HINT and passkey.private, which is the one we're interested in.

This file contains a key that will be used to connect to bandit14 without needing a password. 

However, when trying to connect through bandit13, it displays this message:

(image)

This means we must connect from outside the server. Since the required file is in bandit13, we have to move it to our machine:

```bash
$ scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .   
```
In this case I moved it to the current working directory (.), but it's possible to move it to any path.

Now that it's on my device, I tried to connect to bandit14 using the key file.

```bash
$ ssh -i ./sshkey.private bandit14@bandit.labs.overthewire.org -p 2220
```
This message appeared when trying to access:

(picture)

In order to remove this error, I changed the permissions to read only for the owner (all other permissions were revoked):

```bash
$ chmod 600 sshkey.private
```
After trying again we are now inside bandit14.

## Level 14 -> Level 15
The password can be obtained by using this level's password to port 30000 on localhost.

As stated in the previous level's description, the password for bandit14 is at /etc/bandit_pass/bandit14

```bash
$ cat /etc/bandit_pass/bandit14
```
This outputs bandit14's password.

To connect to localhost, we use telnet. Here we're supposed connect through port 30000:

```bash
$ telnet localhost 30000
```
**All of this while connected to bandit14 server**

This will prompt us for the password, and after pasting it, returns bandit15's.

(picture)

## Level 15 -> Level 16
To get the next password, current level's password should be submitted to port 30001 on localhost using SSL/TLS encryption.

In order to establish this safe connection, we use ```openssl```:

```bash
$ openssl s_client -connect localhost:30001
```

Then we input bandit15's password and get next level's.

## Level 16 -> Level 17
To get next level's password, we have to submit current level's password to a localhost port in the range 31000-32000. We have to find out the one that speaks SSL/TLS.

To find the open port we can use the following command:
``` bash
$ nmap -sC localhost -p 31000-32000
```
Here, -sC is used to run default scripts. This gives us more details of each port.
With this we find out the open port with SSL enabled is 31790.

Now we can send the password:
```bash
$ openssl s_client -connect localhost:31790 -ign_eof
```
Without the ```-ign_eof``` flag, which is triggered by the letter 'k'/'K', every time the password is entered, a KEYUPDATE message is displayed and the password isn't processed.

As a result we get a private key. Save that key in a file on your device and ensure it has as little permissions as possible using ```chmod```.

## Level 17 -> Level 18
The password is the only different line between passwords.old and passwords.new
```bash
$ diff passwords.old passwords.new
```
The right password is the one that's only in _passwords.new_ (the arrow pointing to the right)

This won't allow the connection due to level bandit19.

## Level 18 -> Level 19
Next level's password is in a readme inside the home directory, but .bashrc was modified to log us out when using SSH.

To bypass this modification, we just print the password from the file:
```bash
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 'cat readme'
```
After adding this level's password, we get next level's.

## Level 19 -> Level 20
To access next level, we need to use the setuid binary in the home directory, and find the password in /etc/bandit_pass.

When we execute bandit20-do (run ./bandit20-do), we get the following message:
(image)
If we execute that command, we get bandit20.

This means with this executable we can execute commands as if we were bandit20.

First we find out what's in /etc/bandit_pas:
```bash
$ ./bandit20-do ls /etc/bandit_pass
```
We get the files with all levels' password.

To visualize next level's:
```bash
$ ./bandit20-do cat /etc/bandit_pass/bandit20
```

## Level 20 -> Level 21
Here we have another binary that makes a connection to localhost with the port as an argument, then reads some text from the connection and compares it to bandit20's password. If it's correct, it transmits next level's password.

First we open another session with a connection to bandit20 and establish a connection using netcat:
```bash
$ nc -lvp 1234
```
Here, -l listens for incoming connections, -p specifies the port, and -v produces verbose output.

On the other session we execute the binary, pointing it to the same port:
```bash
$ ./suconnect 1234
```
Afterwards, we paste bandit20's password on the session where ```nc``` is running and check the output.


## Level 21 -> Level 22
A program is running automatically at intervals from cron, whose configuration is in /etc/cron.d/

First, I saw the contents of /etc/cron.d using ```ls```. I first looked into a file called cronjob_bandit22 using ```cat``` and found out it executes a shell script:

(pic)

When displaying that shell script, we can see it actually passes the password onto another file:

(pic)

If we visualize the contents of that temporary file, we can see next level's password

## Level 22 -> Level 23
Again, there is a program executing at regular intervals that's found in /etc/cron.d/

If we look inside cronjob_bandit23 until reaching the shell script (the same way as the previous level), we encounter this:

(pic)

This script generates a hash based on the current user, takes the first column's output, and uses this hash for the temporary file where the password is being pushed.

We can use it to generate the hash, replacing ```$whoami``` with bandit23 and then reading the ```tmp``` file:

```bash
$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
$ cat /tmp/{hash}
```
## Level 23 -> Level 24
A program is running regularly at /etc/cron.d/: cronjob_bandit24. 

Following the same steps as the previous levels, we reach the shell script:

(pic)

This script executes and deletes all files in /var/spool/bandit24/foo.

To do this, we can first generate a secure temporary directory so we can work comfortably and give it full permissions so the program can access it. Then we go to said directory:
```bash
$ mktemp -d
$ chmod 777 /tmp/tmp.{name}
$ cd /tmp/tmp.{name}
```
**Giving full permissions is not recommended, as anyone can read, write, and execute the file/directory**

Since the password is stored in /etc/bandit_pass/bandit24, we can create a shell script that returns its contents and adds it to a file in our /tmp/ directory. We give the file full permissions so the program can access it:
```bash
$ echo -e "#! /bin/bash  cat /etc/bandit_pass/bandit24 > /tmp/tmp.{name}/pass" > getpw.sh
$ chmod 777 getpw.sh
```
Then we create the file where the password will go and give it permissions so the program can write to it:
```bash
$ touch pass
$ chmod 777 pass
```
Finally, we copy the file to /var/spool/bandit24/foo, which is there the files are being executed:
```bash
$ cp getpw.sh /var/spool/bandit24/foo
```

After waiting a while, we can visualize the password inside the file using ```cat```.

## Level 24 -> Level 25
A daemon (program that runs in the background) listens on port 30002 and will give the pass for next level if given last level's plus a secret 4-digit pincode. The only way to obtain it is by going through all 10000 combinations.

To find the right pincode, we'll have to use a method known as brute-forcing, which means trying one by one until we find the right one.
