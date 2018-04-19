# File Structure

Here are the relevant pieces of TFB's file structure:

* .github/
    * PULL_REQUEST_TEMPLATE.md
* [deployment/](#deployment)
    * vagrant-development/
    * etc...
* [frameworks/](#frameworks)
    * Java/
        * [README.md](#language-readmemd)
        * dropwizard/
            * [README.md](#framework-readmemd)
            * [benchmark_config.json (Framework Specific File)](#framework-specific-files)
            * [dropwizard.dockerfile (Framework Specific File)](#framework-specific-files)
            * [source_code](#source-code-file)
            * etc...
        * gemini/
        * etc...
    * Ruby/
    * etc...
* [toolset/](#toolset)
    * [benchmark/](#benchmark)
        * benchmarker.py
        * framework_test.py
    * [continuous/](#continuous)
    * [databases/](#databases)
    * [scaffolding/](#scaffolding)
    * [travis/](#travis)
    * [utils/](#utils)
    * [wrk/](#wrk)
* [.travis.yml](#travisyml)
* [benchmark.cgf(.example)](#benchmarkcfg)



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
information within their individual READMEs. However, there are two 
files that are required by the TFB suite. These are:

* benchmark_config.json
* [test-name].dockerfile

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

#### continuous/

This directory contains the files required to run our continuous benchmarking.

#### databases/

This directory contains the configuration and dockerfiles for the databases we support.

#### scaffolding/

This directory contains a script for quickly bootstrapping a new framework test.

#### travis/

This directory is specifically for travis scripts.

#### utils/

This directory contains a set of helper utilities used by our benchmarking tool.

### .travis.yml

Provides Travis-CI with the relevant information to test TFB. See the 
[Travis-CI](../Project-Information/Travis-CI) section for more information.


# Contents

| Page | Summary |
|:---- |:------- |
[Configuration File](Configuration-File) | Configures set up specifications specific to each user and system.
[Framework Specific Files](Framework-Files) | Files specific to each individual framework for use with the benchmarking suite.
