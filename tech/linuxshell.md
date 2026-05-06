# Linux shell tricks you should know



![](https://labuladong.online/algo/images/souyisou1.png)

**Notice: To meet the demand of many readers, the site now has a [crash-course outline](https://labuladong.online/algo/intro/quick-learning-plan/) — feel free to take a look. Thanks for the support! Also, I recommend reading articles on my [website](https://labuladong.online/algo/) for a better experience.**



**-----------**

I personally love using Linux. Sure, Windows has a better GUI than Linux, but its scripting support is awful. Command-line work felt awkward at first, but once I got used to it I realized that mousing around clicking is what was actually wasting my time...

**This article isn't a man page for specific commands — it pairs usage scenarios with tricky details and small productivity tips for the Linux command line**.

1. The difference between standard input and command arguments.
2. Commands run in the background still exit when you log out of the terminal.
3. The difference between single and double quoted strings.
4. Some commands give "command not found" when used with `sudo`.
5. Avoiding repetitive filenames, paths, and commands; plus a few other tips.

### 1. Difference between standard input and arguments

This one trips people up the most. Specifically: when do we use the pipe `|` and file redirection `>`, `<`, vs. variable substitution `$`?

Example: I have a shell script `connect.sh` that auto-connects to broadband, sitting in my home dir:

```shell
$ where connect.sh
/home/fdl/bin/connect.sh
```

If I want to delete this script with as few keystrokes as possible, what should I do? I once tried this:

```shell
$ where connect.sh | rm
```

That's wrong. The right way:

```shell
$ rm $(where connect.sh)
```

The former tries to feed the output of `where` into `rm`'s standard input. The latter passes the result as a command-line argument.

**Standard input is what `scanf` or `readline` reads in code; arguments are what's passed to your `main` function via the `args` array**.

The earlier article [Linux file descriptors](https://labuladong.online/algo/fname.html?fname=linux进程) explained that pipes and redirections feed data as the program's standard input, while `$(cmd)` reads the output of `cmd` and supplies it as an argument.

In our example, `rm` doesn't accept standard input — it accepts command-line arguments and deletes the matching files. By contrast, `cat` accepts both:

```shell
$ cat filename
...file text...

$ cat < filename
...file text...

$ echo 'hello world' | cat
hello world
```

**If a command makes the terminal block, it accepts standard input; otherwise it doesn't**. For example, run `cat` with no args — the terminal blocks waiting for you to type, then echoes what you typed.

### 2. Running programs in the background

Say you SSH into a server and run a Django web server:

```shell
$ python manager.py runserver 0.0.0.0
Listening on 0.0.0.0:8080...
```

You can now visit Django at the server's IP, but the terminal is blocked — it doesn't respond to anything you type unless you Ctrl-C or Ctrl-/ to kill the python process.

You can append `&` so the terminal isn't blocked and continues to respond. But once you log out, the web service stops too.

If you want the service to keep running after logout, write `(cmd &)`:

```shell
$ (python manager.py runserver 0.0.0.0 &)
Listening on 0.0.0.0:8080...

$ logout
```

**The underlying principle**:

Each terminal is a shell process. Programs you run are child processes of this shell. Normally the shell blocks waiting for the child to exit before accepting your next command. Adding `&` makes the shell not block, so it can continue accepting commands. But regardless, when you close the terminal session, all its child processes exit.

`(cmd &)` reparents `cmd` to the `systemd` daemon — taking `systemd` as its parent — so when you exit the terminal it has no effect on `cmd`.

A similar common trick:

```shell
$ nohup some_cmd &
```

`nohup` works on a similar principle, but in my experience, `(cmd &)` is more reliable.

### 3. Single vs. double quotes

Different shells have subtle differences, but one thing is consistent: **for `$`, `(`, `)`, single-quoted strings don't perform any substitution; double-quoted strings do**.

You can probe shell behavior with `set -x`, which echoes the commands the shell actually runs:

![](https://labuladong.online/algo/images/linuxshell/1.png)

You can see `echo $(cmd)` and `echo "$(cmd)"` produce roughly the same result with subtle differences. Note: the double-quoted-substituted result is automatically wrapped in single quotes; the unquoted form isn't.

**That is, if `$` reads an argument string containing spaces, you should wrap it in double quotes — otherwise things go wrong**.

### 4. `sudo` says command not found

Sometimes a regular-user command works, but adding `sudo` gives "command not found":

```shell
$ connect.sh
network-manager: Permission denied

$ sudo connect.sh
sudo: command not found
```

The reason: `connect.sh` is only on this user's PATH:

```shell
$ where connect.sh 
/home/fdl/bin/connect.sh
```

**When using `sudo`, the system uses the permissions and environment defined for that user in `/etc/sudoers`** — and that PATH doesn't include this script.

Fix: use the full path to the script instead of just its name:

```shell
$ sudo /home/fdl/bin/connect.sh
```

### 5. Tedious to type similar filenames

Brace expansion with comma-separated strings auto-expands — very handy. Examples:

```shell
$ echo {one,two,three}file
onefile twofile threefile

$ echo {one,two,three}{1,2,3}
one1 one2 one3 two1 two2 two3 three1 three2 three3
```

Each item in the braces combines with the surrounding characters. **Note: don't put spaces inside the braces or around commas — that breaks expansion**.

A practical use is expanding arguments for `cp`, `mv`, `rm`, etc.:

```shell
$ cp /very/long/path/file{,.bak}
# copies file to file.bak

$ rm file{1,3,5}.txt
# deletes file1.txt file3.txt file5.txt

$ mv *.{c,cpp} src/
# moves all .c and .cpp files into src/
```

### 6. Tedious to type long path names

**`cd -` returns to the previous directory**:

```shell
$ pwd
/very/long/path
# go back home for a moment
$ cd
$ pwd
/home/labuladong
# go back to the previous directory
$ cd -
$ pwd
/very/long/path
```

**The special token `!$` substitutes the last path of the previous command**:

```shell
# no exec permission yet
$ /usr/bin/script.sh
zsh: permission denied: /usr/bin/script.sh

$ chmod +x !$
chmod +x /usr/bin/script.sh
```

**The special token `!*` substitutes all the file paths from the previous command**:

```shell
# created three script files
$ file script1.sh script2.sh script3.sh

# add exec permission to all of them
$ chmod +x !*
chmod +x script1.sh script2.sh script3.sh
```

**Add commonly used dirs to the `CDPATH` env var** so that when `cd` doesn't find the target in the current dir, it auto-searches `CDPATH`.

For example, if I often go to `/var/log` for logs:

```shell
$ export CDPATH='~:/var/log'
# cd will also search ~ and /var/log

$ pwd
/home/labuladong/musics
$ cd mysql
cd /var/log/mysql
$ pwd
/var/log/mysql
$ cd my_pictures
cd /home/labuladong/my_pictures
```

This is great — saves you from typing full paths. Useful tip!

Note: this works in bash. Other major shells support extending `cd`'s search path, but the variable may not be `CDPATH` — search for the equivalent for your shell.

### 7. Tedious to retype commands

**`!!` substitutes the previous command**:

```shell
$ apt install net-tools
E: Could not open lock file - open (13: Permission denied)

$ sudo !!
sudo apt install net-tools
[sudo] password for fdl:
```

What if a command is long and you forget the args?

**In bash, `Ctrl+R` reverse-searches command history** — most-recent-first.

After Ctrl+R, type `sudo` and bash finds the most recent `sudo` command. Press Enter to run it:

```shell
(reverse-i-search)`sudo': sudo apt install git
```

But this method has drawbacks: it seems to be bash-only (I use zsh, which lacks it), and it only finds one command (the most recent).

For that situation, **the most common method is `history` with a pipe and `grep`**:

```shell
# filter history for commands containing 'config'
$ history | grep 'config'
 7352  ./configure
 7434  git config --global --unset https.proxy
 9609  ifconfig
 9985  clip -o | sed -z 's/\n/,\n/g' | clip
10433  cd ~/.config
```
All your shell commands are recorded — the leading number is the command index. Once you find a command to repeat, you don't even need to copy it: **use `!` + the index to rerun it**:

```shell
$ !7434
git config --global --unset https.proxy
# done
```

I find `history | grep` still typo-heavy. You can put this function in your shell rc (`.bashrc`, `.zshrc`, etc.):

```shell
his()
{
    history | grep "$@"
}
```

Now `his 'some_keyword'` does the trick.

I usually don't use bash. I recommend zsh, which I use myself. It's extensible with plugins — search for setup details.

### Other small tips

**1. `yes` auto-types `y` to confirm**:

When installing some software, you might get interactive prompts:

```shell
$ sudo apt install XXX
...
XXX will use 996 MB disk space, continue? [y/n]
```

We usually just hit `y` repeatedly. Annoying when automating installs — these prompts block.

`yes` helps:

```shell
$ yes | your_cmd
```

This auto-feeds `y` repeatedly so you don't get stuck.

If you've read [Linux file descriptors](https://labuladong.online/algo/fname.html?fname=linux进程), the principle is simple:

Run `yes` alone — it just prints `y` repeatedly. The pipe connects its output to `your_cmd`'s standard input. When `your_cmd` asks something boring, it reads `y` plus newline from stdin — exactly the same effect as your typing `y`.

**2. The special variable `$?` holds the previous command's return code**.

Following C convention, return code 0 means success; non-zero means error. Reading the previous command's return code seems useless interactively, but is very handy for shell scripts.

**Concrete example**: my GitHub repo `fucking-algorithm` — I needed to add "previous / next / TOC" footer links to every Markdown file. Some already had footers, most didn't.

To avoid duplicates, I needed to know whether a Markdown file already had a footer. `$?` plus `grep` does it:

```shell
#!/bin/bash
filename=$1
# check if the bottom contains the keyword
tail | grep '下一篇' $filename
# grep returns 0 on match, non-zero otherwise
[ $? -ne 0 ] && { add footer; }
```

**3. The special variable `$$` is the current process's PID**.

Hardly used interactively, but very useful in scripts. For example, when creating temp files in `/tmp`, naming them is always a chore. Use `$$` to expand to the current PID — PIDs are unique on a system, so the name won't collide and you don't need to remember it.

That's all for today. If you'd like more Linux content, share this article — if numbers look good I'll write more next time.



<hr>
<details class="hint-container details">
<summary><strong>Articles citing this article</strong></summary>

 - [Pitfalls of Linux pipes](https://labuladong.online/algo/fname.html?fname=linux技巧3)

</details><hr>





**＿＿＿＿＿＿＿＿＿＿＿＿＿**

