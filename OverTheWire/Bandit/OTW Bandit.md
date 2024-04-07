We need to connect to **bandit.labs.overthewire.org:2220** through SSH and log in with **bandit<number> to access to the levels.

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

![[Pasted image 20240406155236.png]]

![[Pasted image 20240406155302.png]]

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
![[Pasted image 20240406155658.png]]

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

![[Pasted image 20240406160413.png]]

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

![[Pasted image 20240406160951.png]]

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
![[Pasted image 20240406161523.png]]

We could try `file *`, but it throws an error since all of them starts with a hyphen, and the shell interprets it as an option:
![[Pasted image 20240406161945.png]]

We found the next command:
`find . -maxdepth 1 -type f -exec file {} \; | grep "ASCII text"`

> The way it works:

- `find . -maxdepth 1 -type f`: This command searches for files (not directories) in the current directory (`.`) with a maximum depth of 1 (only in the current directory, not subdirectories).
    
- `-exec file {} \;`: For each file found, `file` command is executed to determine its type.
    
- `| grep "ASCII text"`: The output of the `file` command is then piped (`|`) to `grep`, which filters out only the lines containing "ASCII text", indicating human-readable files.
    

This command will help you identify human-readable files in the current directory even if their filenames start with a hyphen ("-").

![[Pasted image 20240406162232.png]]

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
![[Pasted image 20240406162634.png]]


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

![[Pasted image 20240406170646.png]]

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
![[Pasted image 20240406172849.png]]
`cat /var/lib/dpkg/info/bandit7.password`

Password: z7WtoNQU2XfjmMtWA8u5rN4vzqu4v99S

# Level 7
`
ssh -p 2220 bandit7@bandit.labs.overthewire.org
`

Solution:

`ls` and we already have the `data.txt`where the password is located.
But as we can see, it's bloated.
![[Pasted image 20240407020037.png]]

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



# Level 9
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`


# Level 10
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`


# Level 11
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`


# Level 12
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`


# Level 13
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`

# Level 14
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`

# Level 15
`
ssh -p 2220 bandit8@bandit.labs.overthewire.org
`


