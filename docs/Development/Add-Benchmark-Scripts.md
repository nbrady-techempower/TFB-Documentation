# Installation Scripts

__Guidelines for Writing Installation Scripts__

Whether you are writing new dependency files (e.g. a new language installation
script) or just appending custom code to your framework's `setup.sh`, here 
are some guidelines for proper use:

0. Familiarize yourself with the functions available in `bash_functions.sh`.
1. **Use caching whenever possible** : Use `fw_exists` to avoid re-running 
installations. Note: If you are in the process of writing a new installation, 
you may wish to delete the file checked by `fw_exists` to force an installation 
to run.
2. **Protect against Ctrl-C and partial installations**: Only use `fw_exists`
on objects that exist if the entire installation completed, such as binaries. 
If you use `fw_exists` on a downloaded file, there is no guarantee that 
installation completed. If you use `fw_exists` on an installation directory,
there is no guarantee that compilation completed. Note: Another approach is 
to run your entire compilation, and then move your completed installation to 
a new directory and `fw_exists` on this new directory. 
3. **Specify download file locations**: Use `wget -O` and similar curl options
to avoid having "file.1","file.2", etc when you expect to always have "file"
4. **Understand running bash scripts with errtrace option**: This is likely 
the hardest thing. *If any command in your script returns non-zero, TFB will 
report a potential installation error!* This means no `return 1` statements
except to indicate installation failure, no `mv foo bar` if you're not 
100% sure that foo exists and can be moved to bar, etc. Note that the bash
or operator (||) can be used to avoid errtrace from inspecting a command, 
so you can use something like `mv foo bar || true` to avoid having errtrace
inspect the exit code of the `mv` command. 
5. **Install into folders**: As mentioned above, please try to install your
software into folders. Your installation script will always have the `$IROOT`
variable available, use this to determine what folder your software should
be installed into e.g. `ME=$IROOT/my-software`.
6. **Turn on debugging if you're stuck**: `bash_functions.sh` has an ERR
trap inside it. It's fairly useful, but if you would like more information 
you can uncomment some additional lines in the ERR trap to cause it to print 
an entire bash stack trace e.g. what command in what function in what file on 
what line caused my non-zero status. This is useful, but beware that dragons 
are involved in reading bash stack traces...
7. **Look at the examples!!**: There are tons of example installation scripts 
inside of `toolset/setup/linux`, so please examine them.
