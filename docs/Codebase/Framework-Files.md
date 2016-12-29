There are a few files that are necessary to implement a framework into the 
Framework Benchmarks. All of these files will vary because they're all 
specific and unique to each framework. 

| File | Summary |
|:---- |:------- |
[Setup File](#setup-file) | Installs the framework and its dependencies, configures the framework to the correct database hosts, package framework code, and starts the framework.
[Benchmark Config File](#benchmark-config-file) | Defines test instructions and metadata for the framework benchmarks program.

# Available Bash Variables 

Because it is a bash script, `setup.sh` has these bash variables available:

* FWROOT: Root of project
* IROOT: Root of installation for the current framework
* TROOT: Root directory for the current framework 

# Setup File

The setup file is responsible for installing the framework software and starting the test.
This script is responsible for (among other things):

* Installing any software or tools needed by the framework
* Modifying the framework's configuration to point to the correct database host
* Compiling and/or packaging the code
* Starting the server

The setup file is a shell script that should build the source, make any necessary changes 
to the framework's configuration, and then start the server.

__Installing system software__

Typically, the first thing done is to call `fw_depends` 
to run installations for any necessary software that TFB has already 
created installation scripts for. TFB provides a reasonably wide range of 
core software, so your `setup.sh` may only need to call `fw_depends` to acquire the 
tools it needs. You are also free to add install scripts to the `toolset/setup/linux`  
directory if a tool does not already exist.

To see what TFB provides installations for, look in `toolset/setup/linux`
in the folders `frameworks`, `languages`, `systools`, and `webservers`. 
You should pass the filename, without the ".sh" extension, to fw_depends. 

Note: `fw_depends` does not guarantee dependency management, so 
list software in the proper order e.g. if `foo` depends on `bar`
use `fw_depends bar foo`.

Here are some examples of installing software using `fw_depends`:

```bash
#!/bin/bash

# My framework only needs nodejs
fw_depends nodejs
```

```bash
#!/bin/bash

# My framework needs composer and php7, but since composer depends on php7, I should require php7 first
fw_depends php7 composer
```

__Configuring database connectivity__

By convention, the configuration files used by a framework should specify the database 
server as `localhost` so that developing tests in a single-machine environment can be 
done in an ad hoc fashion, without using the benchmark scripts.

When running a benchmark script, the script needs to modify each framework's configuration
so that the framework connects to a database host provided as a command line argument. 
In order to do this, use stream editor (`sed`) to make modifications prior to 
starting the server.

For example (Java's Wicket Framework):

```bash
sed -i 's|mysql://.*:3306|mysql://'"${DBHOST}"':3306|g' src/main/webapp/WEB-INF/resin-web.xml
```

Note: `args` contains a number of useful items, such as `troot`, `iroot`, `fwroot` (comparable
to their bash counterparts in `setup.sh`, `database_host`, `client_host`, and many others)

Note: Using `localhost` in the raw configuration file is not a requirement as long as the
`sed` call properly injects the database host provided to the benchmark toolset as a command 
line argument.

__A full example__

Here is an example of Wicket's setup file.

```bash
#!/bin/bash

sed -i 's|mysql://.*:3306|mysql://'"${DBHOST}"':3306|g' src/main/webapp/WEB-INF/resin-web.xml

mvn clean compile war:war
rm -rf $RESIN_HOME/webapps/*
cp target/hellowicket-1.0-SNAPSHOT.war $RESIN_HOME/webapps/wicket.war
$RESIN_HOME/bin/resinctl start
```
# Benchmark Config File

The `benchmark_config.json` file is used by our scripts to identify available tests - it should exist at the root of the framework directory.

Here is an example `benchmark_config.json` from the `Compojure` framework. There are two different tests listed for the `Compojure` framework.

    {
      "framework": "compojure",
      "tests": [{
        "default": {
          "setup_file": "setup",
          "json_url": "/compojure/json",
          "db_url": "/compojure/db",
          "query_url": "/compojure/queries/",
          "update_url": "/compojure/updates/",
          "fortune_url": "/compojure/fortunes",
          "plaintext_url": "/compojure/plaintext",
          "port": 8080,
          "approach": "Realistic",
          "classification": "Micro",
          "database": "MySQL",
          "framework": "compojure",
          "language": "Clojure",
          "flavor": "None",
          "orm": "Micro",
          "platform": "Servlet",
          "webserver": "Resin",
          "os": "Linux",
          "database_os": "Linux",
          "display_name": "compojure",
          "notes": "",
          "versus": "servlet"
        },
        "raw": {
          "setup_file": "setup",
          "db_url": "/compojure/raw/db",
          "query_url": "/compojure/raw/queries/",
          "update_url": "/compojure/raw/updates/",
          "fortune_url": "/compojure/raw/fortunes",
          "port": 8080,
          "approach": "Realistic",
          "classification": "Micro",
          "database": "MySQL",
          "framework": "compojure",
          "language": "Clojure",
          "flavor": "None",
          "orm": "Raw",
          "platform": "Servlet",
          "webserver": "Resin",
          "os": "Linux",
          "database_os": "Linux",
          "display_name": "compojure-raw",
          "notes": "",
          "versus": "servlet"
        }
      }]
    }

* `framework:` Specifies the framework name, which is used when the tests are run and within TFB. 
This allows you to call the default test with `toolset/run-tests.py --mode verify --test compojure`, 
or call the other test with `toolset/run-tests.py --mode verify --test compojure-raw`.
* `tests:` A list of tests that can be run for this framework. In many cases, this contains a single element for the "default" test, but additional tests can be specified.  Each test name must be unique when concatenated with the framework name. Each test will be run separately in our Rounds, so it is to your benefit to provide multiple variations in case one works better in some cases.
  * `setup_file:` The location of the [python setup file](#setup-file) that can start and stop the test, excluding the `.py` ending. If your different tests require different setup approachs, use another setup file. 
  * `json_url (optional):` The URI to the JSON test, typically `/json`
  * `db_url (optional):` The URI to the database test, typically `/db`
  * `query_url (optional):` The URI to the variable query test. The URI must be set up so that an integer can be applied to the end of the URI to specify the number of queries to run.  For example, `/query?queries=`(to yield `/query?queries=20`) or `/query/` (to yield `/query/20`)
  * `fortune_url (optional):` the URI to the fortunes test, typically `/fortune`
  * `update_url (optional):` the URI to the updates test, setup in a manner similar to `query_url` described above.
  * `plaintext_url (optional):` the URI of the plaintext test, typically `/plaintext`
  * `port (optional):` The port the server will be listening on, typically `8080`
  * `approach (metadata):` `Realistic` or `Stripped` (see [here](http://www.techempower.com/benchmarks/#section=code&hw=peak&test=json) for a description of all metadata attributes)
  * `classification (metadata):` `Fullstack`, `Micro`, or `Platform`
  * `database (metadata):` `MySQL`, `Postgres`, `MongoDB`, `Cassandra`, `Elasticsearch`, `Redis`, `SQLite`, `SQLServer`, or `None`
  * `framework (metadata):` name of the framework (only used to display information on the results site)
  * `language (metadata):` name of the language
  * `orm (metadata):` `Full`, `Micro`, or `Raw`
  * `platform (metadata):` name of the platform
  * `webserver (metadata):` name of the web-server (also referred to as the "front-end server")
  * `os (metadata):` The application server's operating system, `Linux` or `Windows`
  * `database_os (metadata):` The database server's operating system, `Linux` or `Windows`
  * `display_name (metadata):` How to render this test permutation's name on the results web site.  Some permutation names can be really long, so the display_name is provided in order to provide something more succinct.
  * `versus (optional):` The name of another test (elsewhere in this project) that is a subset of this framework.  This allows for the generation of the framework efficiency chart in the results web site. For example, Compojure is compared to "servlet" since Compojure is built on the Servlets platform.

The [requirements section](../Project-Information/Framework-Tests#requirements) explains the expected response for each URL as well all metadata options available. 
