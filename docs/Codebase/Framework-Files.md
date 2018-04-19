There are a few files that are necessary to implement a framework into the 
Framework Benchmarks. All of these files will vary because they're all 
specific and unique to each framework. 

| File | Summary |
|:---- |:------- |
[Benchmark Config File](#benchmark-config-file) | Defines test instructions and metadata for the framework benchmarks program.
[Test Docker Files](#test-docker-file) | Creates a docker container that installs the test and runs the framework's server.


# Benchmark Config File

The `benchmark_config.json` file is used by our scripts to identify available tests - it should exist at the root of the framework directory.

Here is an example `benchmark_config.json` from the `Compojure` framework. There are two different tests listed for the `Compojure` framework.

    {
      "framework": "compojure",
      "tests": [{
        "default": {
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
This allows you to call the default test with `tfb --mode verify --test compojure`, 
or call the other test with `tfb --mode verify --test compojure-raw`.
* `tests:` A list of tests that can be run for this framework. In many cases, this contains a single element for the "default" test, but additional tests can be specified.  Each test name must be unique when concatenated with the framework name. Each test will be run separately in our Rounds, so it is to your benefit to provide multiple variations in case one works better in some cases.
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

# Test Docker File

In order to install the necessary components for each framework test, a dockerfile named after that test is required. Looking at the `benchmark_config.json` for `compojure` above, a dockerfile for the default test would be called `compojure.dockerfile` and exist at the root level (in this case `frameworks/Clojure/compojure/compojure.dockerfile`.) All frameworks must have a default test with a corresponding dockerfile. The next entry requires a dockerfile named `compojure-raw.dockerfile`.

Each tests' dockerfile should be considered independently. The idea is that a single dockerfile should be a complete install of the framework and run the server in the foreground. If the installation process is the same between two tests, it is encouraged that the code between the two dockerfiles looks the same. Docker will make use of its internal caching system such that the second test will build much faster. 

If you are unfamiliar with writing dockerfiles check out Docker's [documentation](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#sort-multi-line-arguments). Also check out how other framework's build their dockerfiles in our [repo](https://github.com/TechEmpower/FrameworkBenchmarks).
