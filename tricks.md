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
```
python -c "import pty; pty.spawn('/bin/bash')" # Spawn pseudo tty 
```
This allows you to interact with everything like you would with a normal terminal.
No we disable the echo so that our commands are not displayed twice and enable raw tty to pass commands like ctrl-c straight through to our pseudo terminal with:
```bash
# Press ctrl-z to background the reverse shell
stty -echo raw;fg # Run this in your terminal not. (If you run zsh the ;fg is needed otherwise it can be done in two commands)
# You might need to press enter one or two times
export TERM=xterm # Run in the pseudo terminal again to allow clear command
```