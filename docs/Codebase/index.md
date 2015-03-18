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
        * dropwizard/
            * [benchmark_config (Framework Specific File)](#framework-specific-files)
            * [install.sh (Framework Specific File)](#framework-specific-files)
            * [setup.sh (Framework Specific File)](#framework-specific-files)
            * et cetera...
        * gemini/
        * et cetera...
    * Ruby/
    * et cetera...
* [toolset/](#toolset)
    * [benchmark/](#benchmark)
    * [setup/](#setup)
        * linux/
        * sqlserver/
        * windows/
    * [test/](#test)
* [.travis.yml](#travisyml)
* [benchmark.cgf(.example)](#benchmarkcfg)

## config/

Configuration files for various languages, databases, and servers.

## deployment/

Information for deploying the benchmark on a range of cloud or 
self-hosted environments. While you can always use manual deployment, 
automated scripts exist for many scenarios. Explinations of the 
subdirectories are within 
[Summary of Script Directories section](Codebase/Summary-of-Script-Directories).

## frameworks/

This directory contains all of the framework implementations of the 
benchmarking suite tests. The general directory structure is Language 
and then the framework name. The contents for the framework directory 
range between each framework. However, there are a few files that are 
similar. 

For information on each language, there are individual READMEs within 
their respective directory. 

### Framework Specific Files

Most of the files are framework specific and you can find more 
information within their individual READMEs. However, there are three 
files that are required by the TFB suite. These are:

* benchmark_config
* install.sh
* setup.sh

See the section on [Framework Specific Files](Codebase/Framework-Files) 
for more information.

## toolset/

This directory contains the code that TFB uses to automate installing, 
launching, load testing, and terminating each framework.

### benchmark/

This directory contains files that implement or support the benchmark feature:

* `benchmarker.py`: Benchmark execution methods. Called by `run-tests.py`.
* `framework_test.py`: Methods to run a framework's tests. Called by `benchmarker.py`.

These files are not meant for you to run directly. For instructions on running the 
benchmark, please refer to the [benchmarking section](Benchmarking).

### setup/

This directory contains scripts to install the benchmark suite on adequately 
provisioned hosts. It is composed of these directories:

linux: Scripts to setup the Linux server and the Linux client and database server.
sqlserver: Scripts to setup the SQL Server database server.
windows: Scripts to setup the Windows server.

### test/

Script for [Travis-CI](Project-Information/Travis-CI).

## .travis.yml

Provides Travis-CI with the relevant information to test TFB. See the 
[Travis-CI](Project-Information/Travis-CI) section for more information.

## benchmark.cfg

This file sets up user/environment specific information that TFB needs to 
know in order to properly run framework tests. See the 
[Configuration File section](Codebase/Configuration-File) for more information.

# Contents

| Page | Summary |
|:---- |:------- |
[Configuration File](Codebase/Configuration-File) | Configures set up specifications specific to each user and system.
[Framework Specific Files](Codebase/Framework-Files) | Files specific to each individual framework for use with the benchmarking suite.
[Summary of Script Directories](Codebase/Summary-of-Script-Directories) | Guide to directories that assist with scripts for setting up different environments.
