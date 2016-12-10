# Installation Scripts

# Installer File

`installer.py` calls into the shell to perform all installation steps. 
It installs three different sets of software: the server software we 
want to test, the database that we want to use with the server software, 
and the client software that we want to use to generate load for the server.

## Using Install Server Flag

To trigger a server installation, run `toolset/run-tests.py --install server` 
(or `--install all`). _Note: Software is installed onto the current machine, 
you should run the server installation on the machine you plan to run 
servers on during benchmarking. This requires password-less sudo access 
for the current user on the current system._

The software needed depends upon what tests are going to be performed--clearly
if you only wish to test the `go` framework, there is no need to install 
`php`. To avoid installing all server software all the time, you can use 
the `--test` and `--exclude` flags in conjunction with `--install server`. 
Here are some examples: 

```bash
$ # Always run from the root directory of the project
$ cd ~/FrameworkBenchmarks
$ # Install server software to run go
$ toolset/run-tests.py --install server --test go
$ # Install server software for go, install client and database software too
$ toolset/run-tests.py --install all --test go
$ # Install server software for all tests
$ toolset/run-tests.py --install server
$ # Install all software for all tests
$ toolset/run-tests.py --install all
```

Some prerequisite software is needed regardless of what test is being
installed. This includes core tools such as `build-essential`, `curl`, etc.
To install just the prerequisites, set the `--test` flag to empty, as so:

```bash
$ # Install just prerequisite server software
$ toolset/run-tests.py --install server --test ''
```

## Understanding Installation Strategy

When possible, TFB installs all software into isolated folders underneath
the `installs/` directory. This helps to prevent different frameworks from 
interfering with each other. There are two strategies used to 
determine which folder software will be installed into, `pertest` or 
`unified`. With `--install-strategy unified`, all software will be 
installed into folders underneath `installs/`. If multiple frameworks depend
on the same software, it will not be installed twice. This can have large 
benefits for download and compile time, e.g. downloading and compiling 
`php` twice is substantially more work than once. However, it also means that
frameworks are not guaranteed complete isolation - if a framework somehow 
modifies it's installation, then other frameworks using that same dependency
will be affected. 

`--install-strategy unified` results in a directory structure like this: 

```
installs
├── go
├── nodejs
├── php
└── py2
```

With `--install-strategy pertest`, each framework has an isolated installation 
directory and is guaranteed to have it's own copy of installation. This takes
substantially more time for some tests, as large dependencies must be downloaded,
decompressed, and compiled multiple times. 

`--install-strategy pertest` results in a directory structure like this: 

```
installs
└── pertest
    ├── aspnet
    │   └── mono
    ├── aspnet-stripped
    │   └── mono
    ├── dart
    │   ├── go
    │   ├── nodejs
    │   ├── php
    │   └── py2
    └── go
        ├── go
        ├── nodejs
        ├── php
        └── py2
```

Note that both installation strategies use the `installs/` directory, so 
you can always delete this directory to remove all software that was able
to be installed into folders. There is no simple uninstallation strategy for 
software that is installed into standard system folders. 

## How Server Installation Works (Python)

The function `Installer#__install_server_software` is called to install
server software. Here are the steps it follows: 

1. Use `--test` and `--exclude` flags to decide which frameworks need installation. For each test, do the following: 
2. Find installation directory for this test, set environment variable `IROOT`
3. Find root directory for this test, set environment variable `TROOT`
4. Load the functions from `toolset/setup/linux/bash_functions.sh`
5. Execute the `setup.sh` for this test. This normally uses functions 
defined in `bash_functions.sh`, such as `fw_depends`

## Database Installation

The `Installer#__install_database` function uses SFTP to copy a number of 
files to the remote database system, and then uses SSH to log in and 
execute the installation. 

This requires password-less sudo access for the `--database-user` on the 
remote system `--database-host`. 

## Client Installation

The `Installer#__install_client_software` function uses SFTP to copy a number of 
files to the remote system, and then uses SSH to log in and 
execute the installation. 

This requires password-less sudo access for the `--client-user` on the 
remote system `--client-host`. 
