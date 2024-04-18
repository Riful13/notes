We need to connect to **bandit.labs.overthewire.org:2220** through SSH and log in with **bandit<number> to access to the levels.

![](_attachments/Pasted%20image%2020240407155008.png)

EDIT:

--[ Playing the games ]--  
  
 This machine might hold several wargames. 
 If you are playing "somegame", then:  
  
   * USERNAMES are somegame0, somegame1, ...  
   * Most LEVELS are stored in /somegame/. 
   * PASSWORDS for each level are stored in /etc/somegame_pass/.


# Level 0

`
ssh -p 2220 bandit0@bandit.labs.overthewire.org
`

![](_attachments/Pasted%20image%2020240406155236.png)

![](OverTheWire/Bandit/img/Pasted%20image%2020240406155302.png)

`ls`

Then

`cat readme`

And we get our password for the next level, very simple.

`exit`

Password: NH2SXQwcBdpmTEzi3bvBHMM9H66vVXjL



# Level 1
`ssh -p 2220 bandit1@bandit.labs.overthewire.org`

Solution:

`ls`

As we can see, the file we need to read is a Hyphen/Dash. We can't `cat -` as we normally would.
![](_attachments/Pasted%20image%2020240406155658.png)

We have two ways of solving it, either:
`cat ./-` or `cat -- -`, but the last one doesn't work on my system,  I need to test it somewhere else.

`exit`

Password: rRGizSaX8Mk1RTb1CNQoXTcYZWU6lgzi



# Level 2
`
ssh -p 2220 bandit2@bandit.labs.overthewire.org
`

Solution:

`ls`

There's only one file, and it has spaces.

![](_attachments/Pasted%20image%2020240406160413.png)

Of course, `cat spaces in this filename`doesn't work, so we have to change the syntax. (We can also let the autocomplete do it for us)

`cat spaces\ in\ this\ filename`

`exit`

Password: aBZ0W5EmUfAf7kHTQeOwd8bauFJ2lAiG



# Level 3
`
ssh -p 2220 bandit3@bandit.labs.overthewire.org
`

Solution:

`ls`

`cd inhere`

We can see that normal `ls`doesn't show anything in this folder, so we can suppose there's a hidden file or directory here. Therefore we use:

`ls -a`

![](_attachments/Pasted%20image%2020240406160951.png)

`cat hidden`

`exit`

Password: 2EW7BBsr6aMMoJ2HjW067dm8EgX26xNe

# Level 4
`
ssh -p 2220 bandit2@bandit.labs.overthewire.org
`
From now on we should make sure of using `ls -a`.
In this level we find the same folder **inhere**, and once we are in, there is 10 files and we have to find the correct one.

Checking one by one is not the correct approach:
![](_attachments/Pasted%20image%2020240406161523.png)

We could try `file *`, but it throws an error since all of them starts with a hyphen, and the shell interprets it as an option:
![](_attachments/Pasted%20image%2020240406161945.png)

We found the next command:
`find . -maxdepth 1 -type f -exec file {} \; | grep "ASCII text"`

> The way it works:

- `find . -maxdepth 1 -type f`: This command searches for files (not directories) in the current directory (`.`) with a maximum depth of 1 (only in the current directory, not subdirectories).
    
- `-exec file {} \;`: For each file found, `file` command is executed to determine its type.
    
- `| grep "ASCII text"`: The output of the `file` command is then piped (`|`) to `grep`, which filters out only the lines containing "ASCII text", indicating human-readable files.
    

This command will help you identify human-readable files in the current directory even if their filenames start with a hyphen ("-").

![](_attachments/Pasted%20image%2020240406162232.png)

`cat ./-file07`

Password: lrIWWI6bB37kxfiCQZqUdOIYfr6eEeqR


# Level 5
`
ssh -p 2220 bandit5@bandit.labs.overthewire.org
`

Solution:

Same as before:

`ls -a`
`cd inhere`
`ls -a`

We see a bunch of directories.
![](_attachments/Pasted%20image%2020240406162634.png)


> Clues:
> The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:
> - human-readable
> - 1033 bytes in size
> - not executable

We use the find command just as before, lets break it down:
`find /path/to/search -type f -size 1033c ! -executable -exec file {} \; | grep "ASCII text"
`

> [!NOTE] See:
> - `/path/to/search`: This is the directory where you want to start your search. You should replace it with the actual directory you want to search in.
> - `-type f`: Restricts the search to only regular files (not directories).
> - `-size 1033c`: Filters files to be exactly 1033 bytes in size.
> - `! -executable`: Excludes files that are executable.
> - `-exec file {} \;`: Executes the `file` command on each file found to determine its type.
> - `| grep "ASCII text"`: Filters the output to only display files that are identified as "ASCII text" by the `file` command, indicating that they are human-readable.
    

If you have multiple directories to search within, you can simply replace `/path/to/search` with the top-level directory containing all the directories you need to search recursively.

![](_attachments/Pasted%20image%2020240406170646.png)

`cat maybehere07/.file2`

`exit`

Password: P4L4vucdmLnm8I7Vl7jG1ApGSfjYKqJU


# Level 6
`
ssh -p 2220 bandit6@bandit.labs.overthewire.org
`

Solution:

This level doesn't have an **inhere** directory like the last ones, our only option is to go to **home**.

`cd ..`

If we `ls`in this directory, we will see the directory of every bandit level. Now let's see what the clues says.

> The password for the next level is stored **somewhere on the server** and has all of the following properties:
> - owned by user bandit7
> - owned by group bandit6
> - 33 bytes in size

Following the structure from before and using `find / -type f -size 33c -user bandit7 -group bandit6` seems useful and simple enough, but there's a lot of error bloat in the output. We need to add `2>/dev/null`.

`find / -type f -size 33c -user bandit7 -group bandit6 2>/dev/null`
![](_attachments/Pasted%20image%2020240406172849.png)
`cat /var/lib/dpkg/info/bandit7.password`

Password: z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S

# Level 7
`
ssh -p 2220 bandit7@bandit.labs.overthewire.org
`

Solution:

`ls` and we already have the `data.txt`where the password is located.
But as we can see, it's bloated.
![](_attachments/Pasted%20image%2020240407020037.png)

Thankfully, the clue we have for this game is: 
> The password for the next level is stored in the file **data.txt** next to the word **millionth**


Simply, we need to read the file and grep the word. We can use the following sintax:

`strings data.txt | grep -o 'millionth.*'`

Alternatively, we can swap `strings` for `cat`.

To understand it better, `-o`specifies to print the matching part of the line, in this case `millionth`.
We also use `.*`at the end of the word so that it also prints any following characters, `*` means 0 or more characters.

`exit`

Password: TESKZC0XvTetK0S9xNwm25STk5iWrBvP

# Level 8
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`

`sort data.txt | uniq -u`

![](_attachments/Pasted%20image%2020240416130603.png)

Password: EN632PlfYiZbn3PhVK3XOGSlNInNE00t

# Level 9
`
ssh -p 2220 bandit9@bandit.labs.overthewire.org
`

`strings data.txt | grep -o '=.*'`

![](_attachments/Pasted%20image%2020240416131327.png)


Password: G7w8LIi6J3kTb8A7j9LgrywtEUlyyp6s

# Level 10
`
ssh -p 2220 bandit10@bandit.labs.overthewire.org
`
`base64 -d data.txt`


![](_attachments/Pasted%20image%2020240416132122.png)


Password: 6zPeziLdR2RKNdNYFNb6nVCKzphlXHBM
# Level 11
`
ssh -p 2220 bandit11@bandit.labs.overthewire.org
`


`tr 'A-Za-z' 'N-ZA-Mn-za-m' {} data.txt`

Let's break down this command:

- `tr 'A-Za-z' 'N-ZA-Mn-za-m'`: This command tells `tr` to translate characters from the range 'A-Z' and 'a-z' to the range 'N-ZA-Mn-za-m'. This effectively rotates each letter by 13 positions, as 'N' is 13 positions ahead of 'A', 'O' is 14 positions ahead, and so on. Lowercase letters are handled similarly.
    
- ` data.txt`: This part of the command redirects the contents of `data.txt` to `tr`, which then processes it.

![](_attachments/Pasted%20image%2020240416132425.png)

Password: JVNBBFSmZwKKOP0XbFXOoW8chDz5yVRv

# Level 12
`
ssh -p 2220 bandit12@bandit.labs.overthewire.org
`

This one gets tricky. We have to decompress a lot of files constantly like a matrioshka until we find the password.


First of all we have to create a tmp directory so that we can work there without any issues.

![](_attachments/Pasted%20image%2020240416134302.png)

Copy the data.txt file there:

![](_attachments/Pasted%20image%2020240416134324.png)

Change it to .hex:

![](_attachments/Pasted%20image%2020240416134428.png)

Transform the hex file to a bin file:

![](_attachments/Pasted%20image%2020240416134406.png)

From now on we start using the `file` command to know how to proceed:

![](_attachments/Pasted%20image%2020240416134525.png)

It says it's a gzip compressed data, and the file it was before (`data.bin`). Since we want to extract this, we will change the file extension to `.gz`, otherwise we won't be able to use `gzip -d`and decompress it.

![](_attachments/Pasted%20image%2020240416134937.png)


For the next file, it says `bzip2`.
Use `bunzip2`.
![](_attachments/Pasted%20image%2020240416135152.png)

At some point we will get the next type of compression:
![](_attachments/Pasted%20image%2020240416135332.png)

As seen on the image, `tar xf` is all we need, and with all of the tools we used before we will be able to find the password.

![](_attachments/Pasted%20image%2020240416135518.png)

Password: wbWdlBxEir4CaE8LaPhauuOo6pwRmrDw
# Level 13
`
ssh -p 2220 bandit13@bandit.labs.overthewire.org
`
`scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private . `

![](_attachments/Pasted%20image%2020240416162055.png)



![](_attachments/Pasted%20image%2020240416162303.png)

`chmod 700 sshkey.private`


`ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220`



Password: fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq

# Level 14
`
ssh -p 2220 bandit14@bandit.labs.overthewire.org
`
`nc localhost 30000`
`fGrHPx402xGC7U7rXKDaxiWFTOiF0ENq`

![](_attachments/Pasted%20image%2020240416163024.png)

Password: jN2kgmIXJ6fShzhT2avhotn4Zcka6tnt

# Level 15
`
ssh -p 2220 bandit15@bandit.labs.overthewire.org
`

`openssl s_client -connect localhost:30001`
![](_attachments/Pasted%20image%2020240416165956.png)

Password: JQttfApK4SeyHwDlI9SXGR50qclOAil1


# Level 16
`
ssh -p 2220 bandit16@bandit.labs.overthewire.org
`
`nmap localhost -p 31000-32000`

31046/tcp open  unknown
31518/tcp open  unknown
31691/tcp open  unknown
31790/tcp open  unknown
31960/tcp open  unknown

![](_attachments/Pasted%20image%2020240416170221.png)

But if we add `-sV` to the nmap:

![](_attachments/Pasted%20image%2020240416170740.png)

Bingo, the port we need is 31790, so let's do `openssl s_client -connect localhost:31790`
Then the current password: `JQttfApK4SeyHwDlI9SXGR50qclOAil1`

![](_attachments/Pasted%20image%2020240416170952.png)

Save the key and give only our permissions:
![](_attachments/Pasted%20image%2020240416171259.png)


# Level 17
`ssh -i sshkey17.private bandit17@bandit.labs.overthewire.org -p 2220`


![](_attachments/Pasted%20image%2020240416172305.png)

`diff old new`

![](_attachments/Pasted%20image%2020240416172328.png)

Password: hga5tuuCLF6fFzUpnagiMN8ssu9LFrdg

# Level 18

`scp -P 2220 bandit18@bandit.labs.overthewire.org:readme .  `

![](_attachments/Pasted%20image%2020240416172732.png)


Password: awhqfNnAbc1naukrpqDYcF95h7HoMTrC

# Level 19
`
ssh -p 2220 bandit19@bandit.labs.overthewire.org
`

Check the permissions, and we have a binary that can read only as user `bandit20`.
![](_attachments/Pasted%20image%2020240418113736.png)
Thanks to this binary, we can use it to do actions as if we were `bandit20`, since we have execution permissions as `bandit19`.

![](_attachments/Pasted%20image%2020240418114407.png)

Password: VxCazJaVykI6W36BkBU0mJTCM8rR95XT

# Level 20
`
ssh -p 2220 bandit20@bandit.labs.overthewire.org
`

echo -n 'VxCazJaVykI6W36BkBU0mJTCM8rR95XT' | nc -l -p 1234 &

`-n` is to avoid new lines.
`-l` means listening, since we are creating a server on localhost.
`-p` is the port.
`&` this is so that the command runs on the background in the same terminal when we need to run other commands.

After that use `./suconnect 1234` which is the executable we can find in this level, and it will receive the password we sent previously, and we will get the new one back.
![](_attachments/Pasted%20image%2020240418115338.png)

Password: NvEJF7oVjkddltPSrdKEFOllh9V1IBcq


# Level 21
`
ssh -p 2220 bandit21@bandit.labs.overthewire.org
`

We are interested in bandit22 since it's the next level, so we check it out.
![](_attachments/Pasted%20image%2020240418115644.png)

Let's do a cat to the .sh, and we see the file it makes has read permissions to everyone.
![](_attachments/Pasted%20image%2020240418115724.png)


Password: WdDozAdTM2z9DiFEQ2mGlwngMfj4EZff


# Level 22
`
ssh -p 2220 bandit22@bandit.labs.overthewire.org
`

#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget

Password: QYw0Y2aiA672PsMmh9puTQuhoz8SyR2G

# Level 23
`
ssh -p 2220 bandit23@bandit.labs.overthewire.org
`
![](_attachments/Pasted%20image%2020240418122957.png)

`bandit23@bandit:~$ mktemp -d
/tmp/tmp.WxVTrL2krG
bandit23@bandit:~$ cd /tmp/tmp.WxVTrL2krG`


`#!/bin/bash`

`cat /etc/bandit_pass/bandit24 >  /tmp/tmp.WxVTrL2krG/password `

![](_attachments/Pasted%20image%2020240418124005.png)
![](_attachments/Pasted%20image%2020240418123948.png)
![](_attachments/Pasted%20image%2020240418124024.png)

`cp script24.sh /var/spool/bandit24/script24.sh`


Password: VAfGXJ1PBSsPSnvsjI8p759leLZ9GGar

# Level 24
`
ssh -p 2220 bandit24@bandit.labs.overthewire.org
`

# Level 25
`
ssh -p 2220 bandit25@bandit.labs.overthewire.org
`

# Level 26
`
ssh -p 2220 bandit26@bandit.labs.overthewire.org
`

# Level 27
`
ssh -p 2220 bandit27@bandit.labs.overthewire.org
`

# Level 28
`
ssh -p 2220 bandit28@bandit.labs.overthewire.org
`

# Level 29
`
ssh -p 2220 bandit29@bandit.labs.overthewire.org
`

# Level 30
`
ssh -p 2220 bandit30@bandit.labs.overthewire.org
`

# Level 31
`
ssh -p 2220 bandit31@bandit.labs.overthewire.org
`

# Level 32
`
ssh -p 2220 bandit32@bandit.labs.overthewire.org
`

# Level 33
`
ssh -p 2220 bandit33@bandit.labs.overthewire.org
`

# Level 34
`
ssh -p 2220 bandit34@bandit.labs.overthewire.org
`


