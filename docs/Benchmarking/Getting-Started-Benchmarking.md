	This is the getting started guide for __benchmarking__. 
If you're interested in __development__ please visit the 
[getting started guide for development](../Development/Getting-Started).

You will need three computers (or virtual machines) networked together to fill the three benchmark roles of database server, load generation server, and web framework server.

# Linux Server Setup

When setting up a Linux server for benchmarking, you can follow the same steps as [setting up a Linux development environment](../Development/Installation-Guide/).

_Note: If testing a pull request or doing development, it is usually adequate to only use one computer. In that case, your server, client, and database IPs will be 127.0.0.1._

# Benchmark Suite Deployment

__Required Host Roles__

* App (aka web framework) Server
    * Runs the web framework containers
    * Operating system: `Ubuntu Server 16.04 LTS 64-bit`
* Database Server
    * Runs database containers (e.g. `MySQL`, `PostgreSQL`, `MongoDB`, etc).
    * Operating system: `Ubuntu Server 16.04 LTS 64-bit`
* Client (aka load generation) Server
    * Runs the benchmark client container
    * Operating system: `Ubuntu Server 16.04 LTS 64-bit`

## Manual Deployment

__Prerequisites__

Before you get started, the following are the steps you must take on each machine (App, Database, Client) to run the benchmarking suite:

1. Docker installed (you can verify this via `docker run hello-world`)
2. `/lib/systemd/system/docker.service` needs `ExecStart` to have an additional flag: `-H [machine's ip]:2375`
3. `sudo systemctl daemon-reload`
4. `sudo service docker restart` (you can verify this via `sudo service docker status` and see the newly added `-H [machine's ip]:2375` flag)

Since the tests can run for several hours, it helps to set everything up so that once the tests are running, you can leave the machines unattended and don't need to be around to enter ssh or sudo passwords.

__Running the Benchmarking Suite__

Let us assume make some assumptions for the example:

1. You cloned the repository into the home directory of user `techempower`
2. Your App machine has the IP `10.0.0.1`
3. Your Database machine has the IP `10.0.0.2`
4. Your Client machine has the IP `10.0.0.3`

The following command, given the prerequisites here, would run a benchmark for all the tests in the codebase.

```
docker run \
  --network=host \
  --mount type=bind,source=/home/techempower/FrameworkBenchmarks,target=/FrameworkBenchmarks \
  techempower/tfb \
  --server-host 10.0.0.1 \
  --database-host 10.0.0.2 \
  --client-host 10.0.0.3 \
  --network-mode host \
  --quiet
```
