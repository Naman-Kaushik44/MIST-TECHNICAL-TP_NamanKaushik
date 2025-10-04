# Multiple Globs

```naman_kaushik@Ubuntu:~$ ssh -i key hacker@dojo.pwn.college
Connected!
hacker@globbing~multiple-globs:~$ cd /challenge/files
hacker@globbing~multiple-globs:/challenge/files$ /challenge/run *p*
You got it! Here is your flag!
pwn.college{MDyTo_IwvlGXXKXqPG0pynJ93Uq.QXycTO2EDL5IjN0czW}
```
Globs let us reference files without typing them all out, they make it easier for us to write long commands. 
Multiple globs is a feature which allows us to use more than one glob in a single word hence we can look for specific files with minimal effort.
In this challenge of pwn.college, it was asked of us to /challenge/files directory and then run the /challenge/run command there with minimal writing of not more than 3 characters such that includes every word with the letter p in it.

The approach was simple in this challenge, the first glob * means the word shoud start with anything or nothing at all before we have a p and then it could end with anything or nothing at all again which will cover every word with p in it.

#  Split-piping stderr and stdout

This challenge deals with keeping the stderr and stdout separate during the piping process because in most methods like 2>&1, the stderr is redirected to stdout which leads to their mixing.
/challenge/hack in this challenge produces data on stderr and stdout and we were expected to redirect the stderr to /challenge/planet and stdout to /challenge/planet.

During the redirecting error challenge we were taught that we can redirect multiple file descriptors at the same time. This clicked to me after doing a lot of errors, I tried passing through the pipe first and used 2> which of course didn't give me the result since | only pssses stdout.

Again I tried using 2>& 1 which again didn't work because of the restriction on |.

At the end, I decided to not use the pipe and instead used > and 2> to complete the challenge by redirecting the stdout and stderror into their respective target commands as input and got the flag.

```hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack | 2>(/challeneg/the) >(/challenge/planet)
ssh-entrypoint: /challeneg/the: No such file or directory
ssh-entrypoint: 2/dev/fd/63: No such file or directory
Are you sure you're properly redirecting /challenge/hack's standard output into 
'/challenge/planet'?
You must redirect my standard error into '/challenge/the'!
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack | tee 2>(/challeneg/the) >(/challenge/planet)
ssh-entrypoint: /challeneg/the: No such file or directory
tee: 2/dev/fd/63: No such file or directory
This secret data must directly make it to /challenge/planet over my stdout. 
Don't try to copy-paste it; it changes too fast.
1521128053136516794
You must redirect my standard error into '/challenge/the'!
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack 2>&1 | >(/challeneg/the) >(/challenge/planet)
ssh-entrypoint: /challeneg/the: No such file or directory
ssh-entrypoint: /dev/fd/63: Permission denied
Are you sure you're properly redirecting /challenge/hack's standard output into 
'/challenge/planet'?
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack | >(/challenge/planet) 2>& 1 | >(/challenge/the)
ssh-entrypoint: /dev/fd/63: Permission denied
Are you sure you're properly redirecting /challenge/hack's standard output into 
Are you sure you're properly redirecting /challenge/hack's standard error into 
'/challenge/planet'?
'/challenge/the'?
You must redirect my standard error into '/challenge/the'!
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack | >(/challenge/planet) 2> >(/challenge/the)
Are you sure you're properly redirecting /challenge/hack's standard error into 
'/challenge/the'?
You must redirect my standard error into '/challenge/the'!
Are you sure you're properly redirecting /challenge/hack's standard output into 
'/challenge/planet'?
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack | /challenge/planet 2>&1 | /challenge/the
You must redirect my standard error into '/challenge/the'!
You are redirecting standard error *of /challenge/planet*! Instead, you must 
redirect standard error of 'hack'. This must be done *before* any |. The right 
side of the pipe is a different command, with its own redirection, than the 
left side!
Are you sure you're properly redirecting /challenge/hack's standard error into 
'/challenge/the'?
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack | /challenge/planet >(/challenge/the)
You must redirect my standard error into '/challenge/the'!
Are you sure you're properly redirecting /challenge/hack's standard error into 
'/challenge/the'?
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack | /challenge/planet >(/challenge/the)
You must redirect my standard error into '/challenge/the'!
Are you sure you're properly redirecting /challenge/hack's standard error into 
'/challenge/the'?
hacker@piping~split-piping-stderr-and-stdout:~$ /challenge/hack > >(/challenge/planet) 2> >(/challenge/the)
Congratulations, you have learned a redirection technique that even experts 
struggle with! Here is your flag:
pwn.college{MrleifJEzZVCOQ9evIN7erD-5ul.dFDNwYDL5IjN0czW}
```
