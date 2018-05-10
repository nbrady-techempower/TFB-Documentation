To get started developing you'll need to install [docker](https://docs.docker.com/install/) or see our [Quick Start Guide using vagrant](.#quick-start-guide-(vagrant))

1. Clone TFB.

        $ git clone https://github.com/TechEmpower/FrameworkBenchmarks.git

2. Run a test.

        $ ./tfb --mode verify --test gemini

### Explanation of the `./tfb` script

The run script is pretty wordy, but each and every flag is required. If you are using windows, either adapt the docker command at the end of the `./tfb` shell script (replacing `${SCRIPT_ROOT}` with `/c/path/to/FrameworkBenchmarks`), or use vagrant.

The command looks like this: `docker run -it --rm --network tfb -v /var/run/docker.sock:/var/run/docker.sock -v [FWROOT]:/FrameworkBenchmarks techempower/tfb [ARGS]`

- `-it` tells docker to run this in 'interactive' mode and simulate a TTY, so that `ctrl+c` is propagated.
- `--rm` tells docker to remove the container as soon as the toolset finishes running, meaning there aren't hundreds of stopped containers lying around.
- `--network=tfb` tells the container to join the 'tfb' Docker virtual network
- The first `-v` specifies which Docker socket path to mount as a volume in the running container. This allows docker commands run inside this container to use the host container's docker to create/run/stop/remove containers.
- The second `-v` mounts the FrameworkBenchmarks source directory as a volume to share with the container so that rebuilding the toolset image is unnecessary and any changes you make on the host system are available in the running toolset container.
- `techempower/tfb` is the name of toolset container to run

#### A note on Windows:

- Docker expects Linux-style paths. If you cloned on your `C:\` drive, then `[ABS PATH TO THIS DIR]` would be `/c/FrameworkBenchmarks`.
- [Docker for Windows](https://www.docker.com/docker-windows) understands `/var/run/docker.sock` even though that is not a valid path on Windows. [Docker Toolbox](https://docs.docker.com/toolbox/toolbox_install_windows/) **may** not - use at your own risk.

## Quick Start Guide (Vagrant)

Get started developing quickly by utilizing vagrant with TFB. [Git](https://git-scm.com), 
[Virtualbox](https://www.virtualbox.org/) and [vagrant](https://www.vagrantup.com/) are 
required.

1. Clone TFB.

        $ git clone https://github.com/TechEmpower/FrameworkBenchmarks.git

2. Change directories

        $ cd FrameworkBenchmarks/deployment/vagrant

3. Build the vagrant virtual machine

        $ vagrant up

4. Run a test

        $ vagrant ssh
        $ tfb --mode verify --test gemini


## Add a New Test

Either on your computer, or once you open an SSH connection to your vagrant box, start the new test initialization wizard.

        vagrant@TFB-all:~/FrameworkBenchmarks$ ./tfb --new

This will walk you through the entire process of creating a new test to include in the suite.
