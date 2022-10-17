# Linux Terminal

In this section we are going to learn the basics of using the Linux/Unix Shell.  We need to learn how to use this <b>C</b>ommand <b>L</b>ine <b>I</b>nterface (CLI) as it is the fastest and simplest way to manage our enviroment and processes needed for our analysis piplines.  Many **G**raphical **U**ser **I**nterfaces (GUI) will claim to handle these tasks for you but they are generally slower to use than the CLI and the CLI allows far greater automation of complex tasks via scripts.  Finally you will need to master this if you plan to use any supercomputers as they all run Linux and the CLI is often the only way to access the system.  This guide is fairly brief but will give you enough to get up and running.

## What is the Linux Terminal?

Before we begin it will be useful to clarify the language surrounding this section.  You will very often hear the content of this lecture in courses called "Introduction to Linux" or something similar which is not entirely accurate.  Formally, Linix is actually a free, open source, Unix-like operating system kernel.  A kernel is the part of an operating system that is responsible for the lowest level tasks like: memory management, process management/task management, and disk management.  The Linux kernel forms the basis of several operating systems like linux distributions like, Ubuntu, Fedora, Red Hat as well as Android and Chrome.  Mac and iOS use a very similar Unix based kernel so is sometimes said to be based on linux but it is seperate.  Windows has it's own kernel but as of Windows 10 it includes a Linux subsystem that you can enable.  We will not be learning anything about how to install or manage a Linux distribution or kernel in this course

What we are going to be learning is "BASH" which stands for Bourne Again SHell.  BASH is the default login shell for Linux where a shell is a programme that allows the user to provide text commands directly to the operating system.  It is assessed via a **teminal** which is a programme that creates a window for the shell to run in.  If you are on a supercomputer it will almost certainly be running linux and you will only access the system via a terminal running the BASH shell. This is where the confusion between shell, terminal and linux comes from.

BASH is actually a programming language that contains many commands which directly connect to the operating system. If you are on Linux or Mac you can just open a terminal window by default, if you are on Windows you will need to activate the linux subsystem see here: https://itsfoss.com/install-bash-on-windows/

Now let's begin.

## Basics

When you open a terminal window you will get a window with a command prompt and you will usually be in your home directory.  The first thing we need to do is find out where we are so we use `pwd` (<b>p</b>rint <b>w</b>orking **d**irectory):

```
$ pwd
```

This tells us where we are.  Next we need to move to the folder where we want to work but first we need to know where we could go with `ls` (<b>l</b>i<b>s</b>t) which tells us what is in the directory we are in:

```
$ ls
```

We can add options to this command to get more information or to alter the behaviour.  All BASH commands have them and they are added with the "-" prefix.  For example we could add "-a" (**a**ll) to show hidden files (ones begining with a `.`, typically these are hidden for a reason as you shouldn't mess with them.  We will see some examples as we go through the course):

```
$ ls -a
```

To get a list of all possible options for a command you just need add `man` before it to consult the **man**ual pages

```
$ man ls
```
The manual is exited by typing "q".  The options (bit after the dash) can be combined together and input in any order.  For example to get the list in long format, by time, in reverse order, including hidden:

```
$ ls -ltra
```

---
**NOTE -- Permissions**

This list leads us to an important aspect of Linux which you need to consider when working with bash which is permissions.  The permissions for each file is described by the first 10-characters in long format (`drwxrwxrwx` would mean <b>d</b>irectory <b>r</b>ead <b>w</b>rite e<b>x</b>ecute at `user`, then `group`, then `other` level.  If a letter is replaced by "-" then permission is denied for that set of users) which is followed by the owner, group, size, date last accessed, and filename.

The permissions are important as they protect you work and workspace from other users on the system.  Typically you would have `rwx` for user so you can read and edit your files as well as execute them (which just means run them, or for directories/folders open them), `r--` for group so people in your group can see your code but not edit or run it (directories will need `r_x` otherwise they will not be able to open them, omitting `w` means that they can't create new files in your directories) and `---` or `r--` for other depending on taste.  You should avoid making either group or other have `w` as it means that they can edit you stuff (or replace your text with viruses) or in the case of directories, dump unlimited content into your folders.  When you create a file it will default to `-rw-r--r--` as this is safe.  However, if it is a BASH script you will need to change it to `-rwxr--r--` in order to run it.

Permissions can be changed with `chmod` which works on the binary number so `chmod 644 filename` makes `filename` readable by anyone and writable by the owner only. This because `6` in binary is `110` which translates to `rw-` (`1` is on `0` off) for user, and 4 in binary is `100` which translates to `r--` for everyone else so 644 is `-rw-r--r--`.  It also works in symbolic mode where the same command would be `chmod u=rw,go=r filename` see `man chmod` for many other options. The `d` cannot be changed (it's either a directory or it's not and `chmod` can't do much about that).

---

The same but only for files called `hello.py`:

```
$ ls -ltra hello.py
```

or all files ending in `.py`:

```
$ ls -ltra *.py
```
---
**NOTE -- Wildcards**

Here `*` is a wildcard in that it can match any sequence of characters (including none at all).  By characters we mean any symbol at all, not just letters. There are wildcards you can use including:

- `?` which matches one character so `ma?` would match "mat", "map" and "man" but not "mast".  

- `[]` matches any of the contents so `m[uao]m` matches "mum", "mam", "mom".  You can also use dash to indicate a range. So `[0-9]` matches any numeral and `[a-z]` matches any letter.  `!` negates the match so `[!9]` will match all but "9" and `^` will negate all in range so `[^1-4]` will match all but "1,2,3,4". There are also some standard (`POSIX`) sets you can use: [[:lower:]], [[:upper:]], [[:alpha:]], [[:diget:]], [[:alnum:]], [[:punct:]], and [[:space:]] which match: lowercase letters, uppercase letters, upper or lowercase letters, numeric digits, alpha-numeric characters, punctuation characters, and white space characters respectively. 

- {} is a list of things, comma separated without spaces. 

- `\` is used to make special symbols literal. So if you wanted to match `?` you would use `\?`

You can combine any and all of these together, eg:

```
$ ls m?m?s*_[!0][0-9][0-9].py
```

---

Now let's learn some more commands

To navigate which directory we are in we use `cd` (<b>c</b>hange <b>d</b>irectory) command:

```
$ cd some_directory
```

To get back one level:

```
$ cd ..
```

Or many levels:

```
$ cd ../../../../..
```

Or return to the home directory:

```
$ cd
```

We can also do this with 

```
$ cd ~
```

as `~` is a shortcut to the `HOME` shell variable.  It is useful as it can be used with any command as as part of a path, e.g. `cd ~/documents` 

To get to get to the `ROOT` directory we use

```
$ cd /
```

and to go the the directory we were in last we use

```
$ cd -
```

All commands allow tab completion for file/directory names so `cd m<\tab>` would match all directories with `m*` and *complete as much as is unique* which is pretty helpful.  When changing directory to something like "My Documents" we need to treat the space as literal otherwise it thinks you've asked to change into two directories which doesn't make sense.  For this we can either use:

```
$ cd "My Documents"
$ cd My\ Documents
```

For this reason you should not create any directories of filenames with spaces in them when working on a Linux system.  

Next we might want to create or destroy files.  To create a file you can use any command that alters a file as generally they will create the file if it does not exist.  `touch` is a common one to use for this as all it does normally is update the files timestamp. 

```
$ touch christmas_list.txt
```

will create a blank file called `christmas_list.txt`. Interestingly, it also lets you edit the access and modification time stamps to be whatever you want, which is helpful if you need to prove you were busy coding when the diamonds went missing.  The syntax is:

```
$ touch -d 1999-12-25T01:32:24 christmas_list.txt
```

Deleting files is more specific.  Here we use `rm` (<b>r</b>e<b>m</b>ove), for directories use `mkdir` and `rmdir`:

```
$ mkdir tmp
$ cd tmp
$ touch testfile.txt
$ cd ..
$ rm tmp/testfile.txt
$ rmdir tmp
```

These all accept wild cards so `rm *.out` removes all files ending in `.out`.  `rm` will also remove empty directories, and with `-r` option (<b>r</b>ecursive) will delete the directory and everything in it.  You can also disable confirmation with `-f` (<b>f</b>orce).  Be \*VERY\* careful with this. `rm -rf *` will remove all files and directories from this directory up without confirmation.  There is no "Bin" on the command line where files go to while you think about things, **deletions cannot be reversed** once they are gone they are gone forever.  `rm *` will not delete hidden files as `*` will only match non-hidden ones.

We can make copies of files with `cp` (<b>c</b>o<b>p</b>y) where the syntax is `cp file_from file_to`:

```
$ touch a.txt
$ cp a.txt b.txt
$ ls -ltr *.txt
```

or just move them with `mv` (<b>m</b>o<b>v</b>e):

```
$ mv b.txt c.txt
$ ls -ltr *.txt
```

`mv` is for changing the file's directory or renaming files.  `mv` is much quicker than `cp` as `cp` actually duplicates all the data in a new location, `mv` only changes the name and path (diretory it is listed in.  As an aside, directories don't really exist on computers, they are just a tag to help people keep track of them and to aid display so `mv` never actually moves anything).  `mv` can move multiple files using wildcards in the first argument provided the second argment is a destination directory. It cannot rename multiple files via syntax like: `mv *.csv *.txt` which you may think can change the extension of all csv files.

 To rename multiple files there **sometimes** is the command `rename` (it's standard so won't be on all distributions, which is a shame as it's handy.  Check with `man rename`) which has the form (note that as it is non-standard this can also change depending on distributions!)

`rename 'old string' 'new string' 'pattern to match files'`

So to change all our `*.txt` files to `*_old.txt`:

```
$ rename .txt _old.txt *.txt
$ ls -ltr *.txt
```

*If rename is not present in your distribution you can duplicate it with either a script or on a single line with fancy re-direction.  The command: `find . -name "*.txt" -exec sh -c 'mv "$1" "${1%.txt}.csv"' _ {} \;` uses the the `-exec` or execute option to specify a command to run for each file found; the above changes the extension from ".txt" to ".csv".  We will understand more of this command when we look at scripting later*

Finally when we need to find things we can use `find` to locate files in a directory tree.  The syntax is `find` 'where to look' 'options of which `-name` is always wanted' 'filename with optional wildcards':

```
$ find . -name "*.txt"
```

---
NOTE - Wildcard expansion
Here is a real "trap for young players".  If you just typed `find *.txt` you would think that the command worked perfectly but it isn't doing what you think.  When you use wildcards on the command line they are expanded **before** the command is run.  So in this case it expands `*.txt` to match all files in the current directory, then finds each of them in turn. The correct version above will search for any file that matches the `"*.txt"` in this folder *and all sub-directories*. The quotes indicate that we do not want the wildcards expanded but passed to the command as is.  However, there is a difference between single and double quotes with "" meaning we prefer for the wildcards not to be expanded and '' indicating that the must not be expanded at all.  The difference can be important.  


There is additional complexity for users on Mac or Windows where the behaviour can be a little different as they are not strictly Linux systems. (On my mac the `-name` option protects what follows from being expanded so `find . -name *.txt` produces the correct behaviour even thought it shouldn't)

---

To simply read a file, to see what is in it, we can use `cat`, `more` or `less`:

```
$ cat d.txt
$ more d.txt
$ less d.txt
```

`cat` is good for small files as it reads all of them at once and displays the text.  It's main purpose is actually to con<b>cat</b>enate files (join them together).  `less` and `more` both just do a page at a time and have a lot of options with `-n` for line numbers being the most useful.  `head` and `tail` lets you read from the top or bottom of a file and `tail -f` (<b>f</b>ollow) is useful for tracking output to files your code is writing to without locking them (which can cause code to crash.)

If the files are large and we only want to find some particular section we can use `grep` (**g**lobal search for a **r**egular **e**xpression and **p**rint) to find text in a file, eg: 

```
$ grep hello file.txt
```

Will return the lines in `file.txt` which contain the text `hello` anywhere on them.  To use regular expressions you need to add the option -E, which just means <b>E</b>xtended which means it can use regex, **reg**ular **ex**pressions.  

---
NOTE: Regular Expressions

It is important to note that regular expression wildcards are **different** to the wildcards we met earlier!!! Now we have the following:
- `.` matches a single character rather than `?`
- [a-z] and standard sets work the same
- `a?`, `a*`, `a+`, mean match 0 or 1 `a`, 0 or more `a`, 1 or more `a` respectively. `a{N}`, `a{N,}` and `a{N,M}` means match N times, N or more times and N to M times respectively.
- `^` and `$` mean it must start at the beginning or end of a line respectively
- `\<` and `/>` matches empty strings before and after
- `.*` gets you the behaviour of `*` from before as it will match any number of `.`, which is any character.

The difference is that the first set of wildcards are for **Globbing** which matches filenames, and are expanded by the shell.  The second set above are for **Regular Expressions** which are for defining search patterns for text, and are expanded by the function.  If you remember the filename vs text search distinction this should help you to avoid confusing them.  

---

Here also meet the importance of quotes. Suppose we have a file called `greeting.txt` which contains the text `hello`. For the following commands we would see:

```
$ grep hello greeting.txt     ->  hello
$ grep hello *.txt            ->  hello
$ grep hello "*.txt"          ->  grep: *.txt: No such file or directory 
$ grep hello '*.txt'          ->  grep: *.txt: No such file or directory 
```

grep has several useful options `-A, -B, -C` (note capitalisation) followed by `num` will return num lines before, after, before and after, the match respectively.  This can be very useful for finding uses of functions in your code. `-n` will also print the line numbers.  For example:

```
$ grep -n -C 3 'func1' "*.py"
```

will give you all the uses of `func1` in you python files in the current directory, with the preceding and trailing 3 lines, and give you the line numbers where they occour.

Grep can also search for multiple things at once using `|` which here means "or", eg:

```
$ grep -n -C 3 'func1|func2' "*.py"
```

and multiple files with a glob, or just by listing the files

## Command history

Up and down arrows allows you navigate through previously entered commands which you can run again with enter. (Note that if you want to "scroll up" in the terminal to see the previous commands output(and the mouse wheel is not working/ available), you (usually) need to hold the shift key while pressing the up/down arrows, otherwise you will only see the history of the commands.) 

To see your command history you can use the built-in shell command `history` which will display a numbered list of all the commands you have entered previously.  You can then retrieve a command using `!num` which will run the command number `num` from history.  If the list of commands is too long you can also "reverse search" your command history it by pressing `CTRL+R` and typing the part of the command you remember.  You can also use the up and down arrows to navigate from the retrieved command to find others.  

## Redirection

Now that we have seen some basic commands we can learn one of the more powerful aspects of bash, which is redirection.  Redirection lets us pass the output of one command to another. This lets us combine commands to perform some quite sophisticated things.  

We have a selection of tools that let us redirect the input, `STDIN (0)`, and output, `STDOUT (1)` and `STDERR (2)`, of a command.

```
STDIN   -->  | COMMAND |  --> STDOUT
             |         |  --> STDERR
```


Firstly we can redirect `STDOUT` from one command to a file or the contents of a file to a command using `>` and `<` 

```
$ ls -l > output.txt
```

Will list our directory, and write it to a file called `output.txt`, which it will create it if it does not exist (so `> somefile.txt` works the same as `touch somefile.txt`).  We can also append to the end of an existing file using `>>`.

```
$ grep 'func' code.py >> output
```

`>` redirects only `STDOUT` to the file and passes `STDERR` to be displayed in the terminal.  To capture the errors we need to use `2>` (as `2` means `STDERR`), eg:

```
$ ls test.txt 2> errors.txt
```

If we want to capture both `STDOUT` and `STDERR` we can either `&>` or combine the ouput:

```
$ ls *.txt &> output.txt
$ ls *.txt >output.txt 2>&1
```

Where the first combines the two and writes them to `output.txt` and the second directs the `STDOUT` to `output.txt` then directs `STDERR` to wherever `STDOUT` points.  The `&` in front of the `1` makes it mean `STDOUT`, rather than a file called "1".  This expansion is done **before** the command is run so it works the same.  This can be confusing, so let's look at the second case step by step.

```
        Starting location   After > output.txt  After 2>&1
---

STDOUT  /dev/tty            ./output.txt        ./output.txt
STDERR  /dev/tty            /dev/tty            ./output.txt
```

where `/dev/tty` is just a special location which means the terminal.  The other special location is `/dev/null` which delete the output. If we did the reverse `ls *.txt 2>&1 >output.txt` we would have:

```
        Starting location   After 2>&1      >output.txt
---

STDOUT  /dev/tty (1)        /dev/tty (1)    ./output.txt
STDERR  /dev/tty (2)        /dev/tty (1)    /dev/tty (1)
```

Where we have use numbers in brackets to differentiate the identical `/dev/tty` locations.

We can also do the reverse and pass the contents of the file to `STDIN` using `<`

```
$ grep hello < some_file.txt
```

which will search for `hello` in the file `some_file.txt` (you can do this without the `<` and it will still work fine).  Why would you do this as it seems unessecary?  There is a subtle difference between the two which is that `<` anonomises the input.  This means that if we compare the two methods:

```
$ wc -l dog.txt     produces:       1 dog.txt
$ wc -l < dog.txt   produces:       1
```

so second removed the default printing of the filename from `wc` (which does a **w**ord **c**ount).  There are also situations where piping the input to commands simpler. 


The next key action we can do is to redirect the outputof one command to another.  This is called **pipeing** and is done with `|` for example.

```
$ ls -l | grep Jan > January_Files.txt
```

Will list all files in our directory in long format, then use grep to select all those which were last edited in January, then put the output in a file called January_Files.txt.  This is super useful (particularly with grep to search output) and can be used to do a wide range of things.

```
$ history|grep "grep"       (find all grep commands you have used)
$ head -n 100 file.txt | tail -n 20 > lines_81_to_100.txt
$ ls -la | more  (allows us to look at the output of `ls` one page at a time)
```

You can chain together as may command as you want:

```
cat file.txt | sort | uniq | head -n 3 > first_three.txt
```

Which uses `cat` to load the contents of the text file into memory then `sort`s it alphabeticaly, removed duplicates with `uniq`, and selects the first three lines to output to a file.

________________




### Exercise

5. Go into the directory `pycamb` then write a single line command that finds all `.py` files and count how any lines contain `if`
6. Guess the meaning of the following:

```
ls i_do_not_exist *.txt 2> /dev/null | grep [de]
ls i_do_not_exist *.txt 2>&1 | grep [de]
ls i_do_not_exist *.txt 2>&1 1>/dev/null | grep [de]
ls i_do_not_exist *.txt 1>/dev/null 2>&1 | grep [de]
```



find . -name "*.txt" -exec sh -c 'mv "$1" "${1%.txt}.csv"' _ {} \;
Command Breakdown:

'.' => search path starting at current directory marked by ' . '
-name => set find match name (in this case all files that end with `.txt`)
-exec => execute the following command on every match
sh -c => 'exec' creates an independent shell environment for each match
mv "$1" "${1%.txt}.csv" => mv first variable (denoted by $1), which is the current file name, to new name. Here I do a substring match and delete; so take first var again, $1 and use % to delete `.txt` from the string. The `.csv` at the end just concatenates the remaining variable, which in the example below would now be testNumber, with `.csv`, creating the new testNumber.csv filename.
The underscore is a placeholder for $0
The {} is replaced by each (*.txt) filename found by the find command, and becomes $1 to the sh command.
\; marks the end of the -exec command.  You can also use ';' or ";".