This is the getting started guide for __benchmarking__. 
If you're interested in __development__ please visit the 
[getting started guide for development](../Development/Getting-Started).

You will need three computers (or virtual machines) networked 
together to fill the three benchmark roles of database server, 
load generation server, and web framework server. We provide 
a Vagrant script to setup a full benchmark-ready Linux 
environment in Amazon EC2.

If you wish to benchmark a framework on a non-Amazon cloud, e.g. 
Azure or Rackspace, or a framework that requires Windows OS or 
SQL server, there are some limited helper scripts available at 
the moment. We welcome any pull requests along these lines. 
While you can always use *manual deployment*, automated scripts 
exist for many scenarios. Take a look at the 
[Summary of Script Directories section](../Codebase/Summary-of-Script-Directories) 
to figure out which directory has scripts relevant to your use case. 

*Please note: Running this software will make many modifications 
to software and settings, so it's recommended you either use a 
VM or hardware dedicated to this project.*

# Benchmark Suite Setup

[`toolsets/setup`](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/toolset/setup) 
contains scripts to install the benchmark suite on adequately provisioned hosts. 
It is composed of these directories:

* [`linux`](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/toolset/setup/linux): 
Scripts to setup the Linux server and the Linux client and database server.
* [`sqlserver`](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/toolset/setup/sqlserver): 
Scripts to setup the SQL Server database server.
* [`windows`](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/toolset/setup/windows): 
Scripts to setup the Windows server.

The setup process is documented below. If these instructions get out 
of date or require clarification, please 
[open an issue](https://github.com/TechEmpower/FrameworkBenchmarks/issues/new).

_Note: As an alternative to the manual setup process, you can use the 
[automated deployment script](#benchmark-suite-automated-deployment)._

__Prerequisites__

Before starting setup, all the required hosts must be provisioned, with the 
respective operating system and required software installed, and with 
connectivity for remote management (SSH on Linux, RDP and WinRM on Windows).

Refer to [Benchmark Suite Deployment section](#benchmark-suite-deployment) 
for the provisioning procedures documentation.

## Linux Server and Client Setup

_Note: If testing a pull request or doing development, it is usually adequate 
to only use one computer. In that case, your server, client, and database 
IPs will be 127.0.0.1._

## Installing the Framework Benchmark App Server

* Install [Ubuntu 14.04](http://www.ubuntu.com/download/server) with username `tfb`. 
Ensure that OpenSSH is selected when you install. 
If not, run the following command:

        $ sudo apt-get install openssh-server

* If Ubuntu is already installed, run the following command and follow the prompts.

        $ sudo adduser tfb

* Log in as `tfb`.
* Fully update. _Note: If you update the kernel (linux-firmware), it is generally 
a good idea to reboot aftewards._

        $ sudo apt-get update && sudo apt-get upgrade

* Run the command: `sudo visudo`.
* Change line 20 in from `%sudo   ALL=(ALL:ALL) ALL` to `%sudo   ALL=NOPASSWD: ALL`
* Run the following __(Don't enter a password, just hit enter when the 
prompt pops up)__. _Note: This is still necessary if the client and database are 
on the same computer as the server._

        $ ssh-keygen
        $ ssh-copy-id <database ip>
        $ ssh-copy-id <client ip>

* Install git and clone the Framework Benchmarks repository.

        $ sudo apt-get install git
        $ cd ~
        $ git clone https://github.com/TechEmpower/FrameworkBenchmarks.git
        $ cd FrameworkBenchmarks

* Install the server software. This will take a long time.

        $ nohup python toolset/run-tests.py -s <server hostname/ip> -c <client hostname/ip> -u tfb --install-software --install server --list-tests &

* If you want to view the process of installing, do the following. The session can be 
interrupted easily so no need to worry about keeping a connection.

        $ tail -f nohup.out

* Reboot when the install is done.
* Edit your ~/.bashrc file to change the following:
    * Change `TFB_SERVER_HOST=<ip address>` to the server's IP address.
    * Change `TFB_CLIENT_HOST=<ip address>` to the client's ip address.
    * Change `TFB_DATABASE_HOST=<ip address>` to the database's ip address.
    * Change `TFB_CLIENT_IDENTITY_FILE=<path>` to the id file you specified when you 
    ran ssh-keygen (probably /home/tfb/.ssh/id_rsa if you don't know what it is).
    * Run the command `source ~/.bashrc`.
* If you are setting up any other servers, do so before proceeding.
* Run the following commands:

        cd ~/FrameworkBenchmarks
        source ~/.bash_profile
        # For your first time through the tests, set the ulimit for open files
        ulimit -n 8192
        # Most software is installed automatically by the script, but running the mongo command below from
        # the install script was causing some errors. For now this needs to be run manually.
        cd installs && curl -sS https://getcomposer.org/installer | php -- --install-dir=bin
        cd ..
        sudo apt-get remove --purge openjdk-6-jre openjdk-6-jre-headless
        mongo --host database-private-ip < config/create.js

* Before running the tests, do the following:

        $ source ~/.bashrc

## Installing the Framework Benchmark Database Server

* Install [Ubuntu 14.04](http://www.ubuntu.com/download/server) with username `tfb`.
* Log in as `tfb`.
* Fully update. _Note: If you update the kernel (linux-firmware), it is generally a 
good idea to reboot aftewards._

        $ sudo apt-get update && sudo apt-get upgrade

* Run the command: `sudo visudo`.
* Change line 20 in from `%sudo   ALL=(ALL:ALL) ALL` to `%sudo   ALL=NOPASSWD: ALL`
* On the app server, run the following from the FrameworkBenchmark directory (this should 
only take a small amount of time, several minutes or so):

        $ toolset/run-tests.py --install-software --install database --list-tests

## Installing the Framework Benchmark Load Server

* Install [Ubuntu 14.04](http://www.ubuntu.com/download/server) with username `tfb`.
* Log in as `tfb`.
* Fully update. _Note: If you update the kernel (linux-firmware), it is generally a 
good idea to reboot aftewards._

        $ sudo apt-get update && sudo apt-get upgrade

* Run the command: `sudo visudo`.
* Change line 20 in from `%sudo   ALL=(ALL:ALL) ALL` to `%sudo   ALL=NOPASSWD: ALL`
* On the app server, run the following from the FrameworkBenchmark directory 
(this should only take a small amount of time, several minutes or so):

        $ toolset/run-tests.py --install-software --install client --list-tests

You can validate that the setup worked by running a smoke test like this:

    toolset/run-tests.py --max-threads 1 --name smoketest --test servlet-raw --type all -m verify

This should run the verification step for a single framework.

## Windows Server Setup

* Connect to the Windows server via Remote Desktop.
* Copy `installer-bootstrap.ps1` from "toolset/setup/windows" to the server 
(use CTRL-C and CTRL-V).
* Copy your Linux client private key too.
* Right click on the installer script and select `Run with PowerShell`.
* Press Enter to confirm.
* It will install git and then launch `installer.ps1` from the repository, 
which will install everything else.
* The installation takes about 20 minutes.
* Then you have a working console: try `python`, `git`, `ssh`, `curl`, `node` 
etc. and verify that everything works + PowerShell goodies.

The client/database machine is still assumed to be a Linux box. Unless you already 
have the Linux server and client as described below, you can install just the 
client software with this:

    python toolset\run-tests.py -s server-private-ip -c client-private-ip -i "C:\Users\Administrator\Desktop\client.key" --install-software --install client --list-tests

Now you can run tests:

    python toolset\run-tests.py -s server-private-ip -c client-private-ip -i "C:\Users\Administrator\Desktop\client.key" --max-threads 2 --duration 30 --sleep 5 --name win --test aspnet --type all

## SQL Server Setup

* Connect to the SQL Server host via Remote Desktop.
* Run a `Command Prompt` as Administrator.
* Enter this command:

        powershell -ExecutionPolicy Bypass -Command "iex (New-Object Net.WebClient).DownloadString('https://raw.github.com/TechEmpower/FrameworkBenchmarks/master/toolset/setup/sqlserver/setup-sqlserver-bootstrap.ps1')"

* This will configure SQL Server, the Windows Firewall, and populate the database.

Now, when running `run-tests.py`, just add `-d <ip of SQL Server instance>`. This works 
for the (Windows Server-based) `aspnet-sqlserver-raw` and `aspnet-sqlserver-entityframework` 
tests.

## Other hosting

The benchmark suite will work on physical or virtual servers that have local network 
connectivity between them and Internet access for software download.

In general, any private or public hosting can be used, following this procedure:

* Provision the instances (servers) according to the roles, operating system and 
additional software described in the deploment overview.
* Configure networking as needed.
* Set the Linux hosts for passwordless sudo as described in the instructions for 
dedicated hardware.

Specific provisioning instructions and/or scripts for particular hosting or cloud services providers are welcome!

# Benchmark Suite Deployment

__Required Host Roles__

* Client (aka load generation) Server
    * Runs the benchmark client
    * Operating system: `Ubuntu Server 14.04 LTS 64-bit`
* Database Server
    * Hosts databases (e.g. `MySQL`, `PostgreSQL`, `MongoDB`, `SQL Server`, `Cassandra`, `Elasticsearch` or `Redis`).
    * Operating system: `Ubuntu Server 14.04 LTS 64-bit` or `Windows Server 2012 Datacenter 64-bit`
* App (aka web framework) Server
    * Hosts the web frameworks under test
    * Operating system: `Ubuntu Server 14.04 LTS 64-bit` or `Windows Server 2012 Datacenter 64-bit`

## Manual Deployment

__Evaluation downloads__


If you want to run the benchmark on Windows and/or SQL Server, you can download 
evaluation versions from Microsoft:
* [Download Windows Server 2012](http://technet.microsoft.com/en-us/evalcenter/hh670538.aspx)
* [Download SQL Server 2012 SP1](http://www.microsoft.com/betaexperience/pd/SQL2012EvalCTA/enus/default.aspx)

__Prerequisites__

Before you get started, there are a couple of steps you can take to make running 
the tests easier on yourself.

Since the tests can run for several hours, it helps to set everything up so that 
once the tests are running, you can leave the machines unattended and don't need 
to be around to enter ssh or sudo passwords.

1. Enable passwordless SSH access to localhost 
([search Google for help](https://www.google.com/#hl=en&q=passwordless%20SSH%20access), 
yielding references such as these: 
[article 1](http://hortonworks.com/kb/generating-ssh-keys-for-passwordless-login/), 
[article 2](http://superuser.com/questions/336226/how-to-ssh-to-localhost-without-password), 
[article 3](https://help.ubuntu.com/community/SSH/OpenSSH/Keys))
2. Enable passwordless sudo access ([Google for help](https://www.google.com/#hl=en&q=passwordless%20sudo)).

After this, clone our repository and run `toolset/run-tests.py --help` for 
detailed guidance. You can also refer to the 
[Benchmark Suite Setup section](#benchmark-suite-setup) for more information. 

__Installation and Usage Details__

If you choose to run TFB on your own computer, you will need to install 
passwordless SSH to your `load server` and your `database server` from 
your `app server`. You will also need to enable passwordless sudo access
on all three servers. If you are only planning to use *verify* mode, then
all three servers can be the same computer, and you will need to ensure
you have passwordless sudo access to `localhost`. 

For all Linux framework tests, we use 
[Ubuntu 14.04](http://www.ubuntu.com/download/server), so it is recommended 
you use this for development or use. Furthermore, the load server is 
Linux-only, even when testing Windows frameworks.

## Benchmark Suite Automated Deployment

The [`deployment/common`](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/deployment/common) 
directory contains tools and documentation to automatically deploy the 
benchmark suite on an environment already provisioned according to the 
instructions in the [Benchmark Suite Deployment Section](#benchmark-suite-deployment).

You can use this as an easier alternative to the 
[manual setup process](#benchmark-suite-setup).

_Note: This script will be automatically executed by other scripts, such as the 
[Windows Azure deployment script](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/deployment/azure). 
In this case, you don't need to configure or execute this script yourself._

__Prerequisites__

The tooling is cross-platform, based on bash scripts and Unix tools.

* On Windows, run the PowerShell script `InstallCygwin.ps1` 
(in the toolset/deployment/common directory) to install Cygwin.
* On Linux and OS X, these tools are natively available.

__Configuration__

The deployment parameters are provided through a configuration 
file that you can create in a temporary directory.

This is an example file:

    $ cat ~/tmp/deployment-configuration.sh
    BENCHMARK_LINUX_CLIENT=wfb08291038cli.cloudapp.net
    BENCHMARK_LINUX_CLIENT_IP=10.32.0.4
    BENCHMARK_LINUX_SERVER=wfb08291038lsr.cloudapp.net
    BENCHMARK_LINUX_SERVER_IP=10.32.0.12
    BENCHMARK_LINUX_USER=ubuntu
    BENCHMARK_SSH_KEY=/home/user/.ssh/id_rsa-wfb08291038
    BENCHMARK_WINDOWS_SERVER=wfb08291038wsr.cloudapp.net
    BENCHMARK_WINDOWS_SERVER_USER=wfb08291038wsr\Administrator
    BENCHMARK_SQL_SERVER=wfb08291038sql.cloudapp.net
    BENCHMARK_SQL_SERVER_USER=wfb08291038sql\Administrator
    BENCHMARK_WORKING_DIR=/home/user/tmp/wfb08291038
    BENCHMARK_REPOSITORY=https://github.com/TechEmpower/FrameworkBenchmarks.git
    BENCHMARK_BRANCH=master

The parameters are:

* __BENCHMARK_LINUX_CLIENT__: The DNS name or IP address to be used to connect to the Linux client host for administrative purposes. Usually this will be an address on the public Internet.
* __BENCHMARK_LINUX_CLIENT_IP__: The IP address that the other benchmark hosts should use to connect to the Linux client host. Usually this will be a private (internal) network address.
* __BENCHMARK_LINUX_SERVER__: The DNS name or IP address to be used to connect to the Linux server host for administrative purposes. Usually this will be an address on the public Internet.
* __BENCHMARK_LINUX_SERVER_IP__: The IP address that the other benchmark hosts should use to connect to the Linux server host. Usually this will be a private (internal) network address.
* __BENCHMARK_LINUX_USER__: The name of the administrative user on both the Linux client and the Linux server.
* __BENCHMARK_SSH_KEY__: The private key of the administrative user on both the Linux client and the Linux server.
* __BENCHMARK_WINDOWS_SERVER__: The DNS name or IP address to be used to connect to the Windows Server host for administrative purposes. Usually this will be an address on the public Internet.
* __BENCHMARK_WINDOWS_SERVER_USER__: The fully qualified name of the administrative user on the Windows Server host.
* __BENCHMARK_SQL_SERVER__: The DNS name or IP address to be used to connect to the SQL Server host for administrative purposes. Usually this will be an address on the public Internet.
* __BENCHMARK_SQL_SERVER_USER__: The fully qualified name of the administrative user on the SQL Server host.
* __BENCHMARK_WORKING_DIR__: An existing directory where the deployment scripts can save temporary and log files.
* __BENCHMARK_REPOSITORY__: The Git repository of the benchmark suite. Use a custom repository address if you want to deploy from a fork. Otherwise use the official one (https://github.com/TechEmpower/FrameworkBenchmarks.git).
* __BENCHMARK_BRANCH__: The Git branch of the benchmark suite. Use a custom branch name if you want to deploy from another branch. Otherwise use the official one (master).

__Deployment__

On Linux or OS X, considering the framework was cloned at "~/", execute these commands in a shell:

    cd ~/FrameworkBenchmarks
    nohup toolset/deployment/common/deployment.sh &> deployment.log &
    tail -f deployment.log

On Windows, first install Cygwin running `InstallCygwin.ps1` then, considering the framework 
was cloned at "C:\", execute these commands in a command prompt:

    C:
    cd \FrameworkBenchmarks
    bash -o igncr toolset/deployment/common/deployment.sh

Notice that the deployment process will take several hours. During this time the machine that 
is running it will have to be online, otherwise the deployment process will be interrupted 
and you'll probably have to start over from clean hosts.

__Diagnostics__

The deployment script will output progress messages. You can watch these messages in the 
terminal, or redirect them to an output file (such as on the Linux example above) and watch 
this file.

The deployment process is composed of several steps. For each step, one or more log files 
may be created. Their pathname will be shown and you can open another terminal to watch the 
progress of that step, like this:

    tail -f /home/user/tmp/wfb08291038/lsr-step-1.log

The first step (lsr-step-1, for "Linux server step 1") will install the dependencies on 
the Linux server and, from it, on the Linux client. This is a long process and you can see 
an overview of its progress with a command like this:

    grep '^INSTALL' /home/user/tmp/wfb08291038/lsr-step-1.log
    
Or, for watching continuously:

    tail -f /home/user/tmp/wfb08291038/lsr-step-1.log | grep '^INSTALL' 

You can review this step for installation errors with this command:

    grep '^INSTALL ERROR' /home/user/tmp/wfb08291038/lsr-step-1.log

In case one step fails, you'll usually find the error message at the end of the log, and 
you can review it like this:

    tail deployment.log
    tail /home/user/tmp/wfb08291038/lsr-step-2.log

If there are errors, the safest option would be to start the deployment process all over, 
provisioning new and clean hosts. The benchmark suite and its tests have a great number of 
dependencies that have to be installed in a particular order. If one of these steps fail, 
it may compromise the entire suite or an entire class of tests.

With that said, depending on the kind of error, you could either ignore it (for instance if 
it compromises a test you don't need to run) or attempt to solve it.

Another option would be to perform the setup manually as described in the 
[Benchmark Suite Setup section](#benchmark-suite-setup).

If you find that the automated deployment process is not working as it should please 
[open an issue](https://github.com/TechEmpower/FrameworkBenchmarks/issues/new), 
attaching a link to all relevant log files.
