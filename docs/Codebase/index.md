# File Structure

Here are the relevant pieces of TFB's file structure:

* [config/](#config)
    * cassandra/
* [deployment/](#deployment)
    * azure/
    * vagrant-development/
    * et cetera...
* [frameworks/](#frameworks)
    * Java/
        * [README.md](#language-readmemd)
        * dropwizard/
            * [README.md](#framework-readmemd)
            * [benchmark_config.json (Framework Specific File)](#framework-specific-files)
            * [install.sh (Framework Specific File)](#framework-specific-files)
            * [setup.sh (Framework Specific File)](#framework-specific-files)
            * [source_code](#source-code-file)
            * et cetera...
        * gemini/
        * et cetera...
    * Ruby/
    * et cetera...
* [toolset/](#toolset)
    * [benchmark/](#benchmark)
        * benchmarker.py
        * framework_test.py
    * [setup/](#setup)
        * linux/
            * [frameworks/ (Installation Scripts Directories)](#installation-script-directories)
            * [languages/ (Installation Scripts Directories)](#installation-script-directories)
            * [systools/ (Installation Scripts Directories)](#installation-script-directories)
            * [webservers/ (Installation Scripts Directories)](#installation-script-directories)
            * [installer.py](#installerpy)
        * sqlserver/
        * windows/
    * [test/](#test)
* [.travis.yml](#travisyml)
* [benchmark.cgf(.example)](#benchmarkcfg)

### config/

Configuration files for various languages, databases, and servers.

### deployment/

Information for deploying the benchmark on a range of cloud or 
self-hosted environments. While you can always use manual deployment, 
automated scripts exist for many scenarios. Explinations of the 
subdirectories are within 
[Summary of Script Directories section](Summary-of-Script-Directories).

### frameworks/

This directory contains all of the framework implementations of the 
benchmarking suite tests. The general directory structure is Language 
and then the framework name. The contents for the framework directory 
range between each framework. However, there are a few files that are 
similar. 

For information on each language, there are individual READMEs within 
their respective directory. 

#### Language README.md

Because the information for each language varies so much, each language 
should have its own README. This should include information like which 
software versions are used, special instructions for adding a new 
framework in that language, and places where you can get help specific 
to that language. See the [Language README Formatting Guide](../Development/Readme-Formats#language-readmes) 
for a more detailed guide.

#### Framework README.md

Because the information for each framework varies so much, each framework 
should have its own README. This should include information like which 
software versions are used, special instructions for adding a new 
variation in that framework, and places where you can get help specific 
to that framework. See the [Framework README Formatting Guide](../Development/Readme-Formats#framework-readmes) 
for a more detailed guide.

#### Framework Specific Files

Most of the files are framework specific and you can find more 
information within their individual READMEs. However, there are three 
files that are required by the TFB suite. These are:

* benchmark_config.json
* install.sh
* setup.sh

See the section on [Framework Specific Files](Framework-Files) 
for more information.

#### Source Code File

The file named `source_code` within each framework is used to count the lines 
of code in the project - each file listed in source_code has its lines counted 
(we're using [CLOC](http://cloc.sourceforge.net/) to provide this functionality)
and the result are include in `results.json` under the rawData > slocCounts 
keys, as shown here:

    "rawData": {
      "slocCounts": {
        "mojolicious": 72, 
        "scruffy": 47, 
        "nodejs": 217, 
        "spring": 242, 
        "cpoll-cppsp": 82, 
        "slim": 1889, 
        "dropwizard": 293, 
        "revel": 258, 
        "dancer": 32, 

TFB does not use the numbers in any of the graphs but we include them for 
other consumers of the data.

_Note: The keys used are framework names (e.g. ringo) and not the framework 
test names (e.g. ringojs-raw)._

### toolset/

This directory contains the code that TFB uses to automate installing, 
launching, load testing, and terminating each framework.

#### benchmark/

This directory contains files that implement or support the benchmark feature:

* `benchmarker.py`: Benchmark execution methods. Called by `run-tests.py`.
* `framework_test.py`: Methods to run a framework's tests. Called by `benchmarker.py`.

These files are not meant for you to run directly. For instructions on running the 
benchmark, please refer to the [benchmarking section](../Benchmarking).

#### setup/

This directory contains scripts to install the benchmark suite on adequately 
provisioned hosts. It is composed of these directories:

linux: Scripts to setup the Linux server and the Linux client and database server.
sqlserver: Scripts to setup the SQL Server database server.
windows: Scripts to setup the Windows server.

##### Installation Script Directories

These directories help organize the installation scripts into four 
cagegories (frameworks, languages, systools, and webservers), 
which are called by [`installer.py`](#installerpy) when tests are run. 
Visit the [installation script section](../Development/Add-Benchmark-Scripts#installation-scripts) 
for guidelines on writing an installation script, or visit the 
[installer file section](Setup-Files#installer-file) for 
more information on how the installer scripts are called.

##### installer.py

The `installer.py` file calls into the shell to perform all installation steps. 
It installs three different sets of software, the server software we want to 
test, the database that we want to use with the server software, and the client 
software that we want to use to generate load for the server. The 
[installer file section](Setup-Files#installer-file) gives 
an overview of how this installation process works.

#### test/

Script for [Travis-CI](../Project-Information/Travis-CI).

### .travis.yml

Provides Travis-CI with the relevant information to test TFB. See the 
[Travis-CI](../Project-Information/Travis-CI) section for more information.

### benchmark.cfg

This file sets up user/environment specific information that TFB needs to 
know in order to properly run framework tests. See the 
[Configuration File section](Configuration-File) for more information.

# Contents

| Page | Summary |
|:---- |:------- |
[Configuration File](Configuration-File) | Configures set up specifications specific to each user and system.
[Framework Specific Files](Framework-Files) | Files specific to each individual framework for use with the benchmarking suite.
[Summary of Script Directories](Summary-of-Script-Directories) | Guide to directories that assist with scripts for setting up different environments.
