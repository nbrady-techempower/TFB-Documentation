If you're looking to quickly get started developing using the vagrant development environment, consider the [vagrant development environment guide](#vagrant-development-environment). 

Take a look at the [Summary of Script Directories section](../Codebase/Summary-of-Script-Directories) to figure out which directory has scripts relevant to your use case (development or benchmarking).

#Installation Basics

In order for SSH to work appropriately, the user on **each machine** will need to be 
set up to have passwordless sudo access.

**Setting up the `user`**

```bash
sudo vim /etc/sudoers
```

You will need to change the line that reads `%sudo   ALL=(ALL:ALL) ALL` to 
`%sudo   ALL=(ALL:ALL) NOPASSWD: ALL`. You should be able to exit your shell, ssh 
back in and perform `sudo ls` without being prompted for a password.

**NOTE** Again, you will have to do this on every machine: server, database, client.

**Setting up SSH**

You will need to also be able to SSH onto each of the 3 machines from any of the
3 machines and not be prompted for a password. We use an identity file and authorized_keys
file to accomplish this.

```bash
ssh-keygen -t rsa
```

This will prompt you for various inputs; leave them all blank and just hit 'enter'.
Next, you will want to allow connections identified via that key signature, so add
it to your authorized keys.

```bash
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

Next, you will need these exact same files to exist on the other 2 machines.
If you properly set up your user account on all 3 machines, then this will not prompt
you for a password (if it does, go back to "Setting up the `user`" and fix it).

```bash
# Export some variables so you can copy/paste the rest.
export TFB_SERVER_USER=[your server user]
export TFB_SERVER_HOST=[your server ip]
export TFB_DATABASE_USER=[your database user]
export TFB_DATABASE_HOST=[database ip]
export TFB_CLIENT_USER=[your client user]
export TFB_CLIENT_HOST=[client ip]
# Set up the database machine for SSH
cat ~/.ssh/id_rsa.pub | ssh $TFB_DATABASE_USER@$TFB_DATABASE_HOST 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
scp ~/.ssh/id_rsa $TFB_DATABASE_USER@$TFB_DATABASE_HOST:~/.ssh/id_rsa
scp ~/.ssh/id_rsa.pub $TFB_DATABASE_USER@$TFB_DATABASE_HOST:~/.ssh/id_rsa.pub
# Set up the client machine for SSH
cat ~/.ssh/id_rsa.pub | ssh $TFB_CLIENT_USER@$TFB_CLIENT_HOST 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys'
scp ~/.ssh/id_rsa $TFB_CLIENT_USER@$TFB_CLIENT_HOST:~/.ssh/id_rsa
scp ~/.ssh/id_rsa.pub $TFB_CLIENT_USER@$TFB_CLIENT_HOST:~/.ssh/id_rsa.pub
```

Now, test it all out, you should be able to execute all of the following without
being prompted for a password. **NOTE** The first time you SSH to these machines
(read: in this step) you will be prompted to accept the signature - do it.

```bash
# Test your database SSH setup
ssh $TFB_DATABASE_HOST
# Accept the signature
# You are connected to the database machine!
sudo ls
# This should NOT prompt for a password and list the directory's contents
# If this is not true, go back to "Setting up the `user`" and fix it
exit
# Test your client SSH setup
ssh $TFB_CLIENT_HOST
# Accept the signature
# You are connected to the client machine!
sudo ls
# We also need to test that we can SSH back to the server machine
ssh [enter your server ip again]
# Accept the signature
# You are connected to the server machine!
sudo ls
# If this works, you are golden!
exit
# Back on client
exit
# Back on initial ssh connection to server machine
```

**Setting up prerequisites**

The suite requires that a few libraries and applications are installed in order to run.
First, clone our repository.

```bash
git clone https://github.com/TechEmpower/FrameworkBenchmarks.git
cd FrameworkBenchmarks
source toolset/setup/linux/prerequisites.sh
```

To install TFB components onto the various servers, you must provide
basic login details for each server (e.g. usernames, IP addresses, 
private key files). While these details can all be passed 
using command line flags, it's easier to use a configuration 
file and avoid having huge flag lists in your commands. 

```bash
cp benchmark.cfg.example benchmark.cfg
vim benchmark.cfg
```

You will need to change, at a minimum, the following:

* `client_host` Set this to the IP address of your client machine
* `client_identity_file` Set to `/home/[username]/.ssh/id_rsa`
* `client_user` Set to your username
* `database_host` Set this to the IP address of your database machine
* `database_identity_file` Set to `/home/[username]/.ssh/id_rsa`
* `database_user` Set to your username
* `server_host` Set this to the IP address of your server machine

Now in your `/etc/hosts` file add the following lines, replacing `127.0.0.1` with the appropriate IPs:

```
127.0.0.1 TFB-database
127.0.0.1 TFB-client
127.0.0.1 TFB-server
```

At this point, you should be able to run a benchmark of the entire
suite or selectively run individual benchmarks. Additionally, you
can test your setup by running a verification on one of the stable
frameworks. Since we wrote it, we tend to test with `gemini`.

From the `FrameworkBenchmarks` directory run:

```bash
tfb --mode verify --test gemini
```

You can find the results for this verification step under the directory:
`results/latest/logs/gemini`. There should be an `err` and an `out`
file.

**Tuning your Machine (optional)**

In some cases, particularly if you are running the tests on high-performance machines, you may start to see high latency and poor results due to certain system configurations throttling the power of your hardware. Warning signs to look out for include:

* High max latency for the wrk tests with a large number of concurrent connections when running a high-performance framework (libreactor, undertow, gemini, etc.)
* Requests/second decreasing rather than increasing as the number of concurrent connections goes up
* When running htop during a test, you see very high (~90%) kernal time (the red) and relatively low (~10%) program time (the green)
* Running an iperf test between two of your machines with a fairly high number of concurrent connections (using the -P16 or -P32 flag) shows a drop in overall throughput
* You are generally seeing results that are much lower than you would expect, given the hardware you are running your 
tests on

To address these issues, we've had the most success with the [Ubuntu Tuned](https://github.com/edwardbadboy/tuned-ubuntu) tool configured to use the `latency-performance` profile. You *should* only have to use this on the App Server machine. First, install it on your system:
```bash
sudo apt-get install git git-core build-essential
sudo apt-get install rpm python-decorator python-dbus python-gobject python-pyudev python-configobj
git clone https://github.com/edwardbadboy/tuned-ubuntu.git
cd tuned-ubuntu
sudo make install
```

Restart your machine. Then start up the service and set your profile:
```bash
sudo service tuned start
sudo tuned --profile latency-performance
```

And that should do it! 

# Vagrant Development Environment

The simplest way to begin development is to set up [vagrant](https://www.vagrantup.com/). 

Here's a short example: 
    
```bash
# Clone the project, then move into the right directory 
$ cd ~/FrameworkBenchmarks/deployment/vagrant-development
# Turn on the VM. Takes at least 20 minutes
$ vagrant up
# Enter the VM, then run a test
$ vagrant ssh
vagrant@TFB-all:~$ cd ~/FrameworkBenchmarks
vagrant@TFB-all:~/FrameworkBenchmarks$ tfb --mode verify --test gemini
# For non-Windows users only (will break Windows environments):
# If the local version of your code does not mirror the code in your virtual machine, then run the following:
# Note: the following lines must be run each time "vagrant up" is run, even after "vagrant halt"
vagrant@TFB-all:~/FrameworkBenchmarks$ sudo mount.vboxsf -o uid=1000,gid=1000 FrameworkBenchmarks ~/FrameworkBenchmarks
vagrant@TFB-all:~/FrameworkBenchmarks$ mkdir -p /tmp/TFB_installs
vagrant@TFB-all:~/FrameworkBenchmarks$ mkdir -p ~/FrameworkBenchmarks/installs
vagrant@TFB-all:~/FrameworkBenchmarks$ sudo mount -o bind /tmp/TFB_installs ~/FrameworkBenchmarks/installs
```

## Vagrant Snapshots

After you initially setup the virtual machine using `vagrant up`, it is often smart to create a snapshot
of the VM state at that point. This allows you to easily reset the VM to a clean state without
having to destroy and recreate it. There are multiple vagrant plugins to enable snapshotting. 
Here is one options: 

```bash
# Create the VM
$ vagrant up
# Before doing anything else, install the snapshot plugin
$ vagrant plugin install vagrant-vbox-snapshot
# Now take the initial snapshot (vm name is default)
$ vagrant snapshot take default initial
Taking snapshot initial
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
# Use VM as normal
$ vagrant ssh
```

## Vagrant VirtualBox Guest
Optional: You can automatically update VirtualBox Guest Additions to the current version of VirtualBox that is being used every time you enter 'vagrant up'. This will also allow you to keep all of your vagrant mounts after kernel upgrades in VirtualBox. Run the following one time after 'cd ~/FrameworkBenchmarks/deployment/vagrant-development' (must already have RVM installed):
```bash
# To install the plugin:
$ vagrant plugin install vagrant-vbguest
# From now on every 'vagrant up' will check and install the correct
# guest additions, but you can also force an update this way:
$ vagrant plugin update vagrant-vbguests
# You can also force vagrant to reload and recheck for updates this way:
$ vagrant reload
```
