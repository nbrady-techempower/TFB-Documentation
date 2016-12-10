#Terminology

|Term|Description|
| --:|:--------- |
|framework|We use the word framework loosely to refer to any HTTP stack—a full-stack framework, a micro-framework, or even a web platform such as Rack, Servlet, or plain PHP.|
|permutation| A combination of attributes that compose a full technology stack being tested (such as node.js paired with MongoDB or node.js paired with MySQL). Some frameworks have seen many permutations contributed by the community; others only one or two.|
|test type|One of the workloads we exercise, such as JSON serialization, single-query, multiple-query, fortunes, data updates, and plaintext.|
|test|An individual test is a measurement of the performance of a permutation's implementation of a test type. For example, a test might be measuring Wicket paired with MySQL running the single-query test type.|
|implementation|Sometimes called "test implementations," these are the bodies of code and configurations created to test permutations according to the requirements. These are frequently contributed by the developer community. Basically, together with the toolset, test implementations are the meat of this project.|
|toolset|A set of Python scripts that run our tests.|
|run|An execution of the benchmark toolset across the suite of test implementations, either in full or in part, in order to capture results for any purpose.|
|preview|A capture of data from a run used by project participants to sanity-check prior to an official round.|
|round|A posting of "official" results on this web site. This is mostly for ease of consumption by readers and good-spirited and healthy competitive bragging rights. For in-depth analysis, we encourage you to examine the source code and run the tests on your own hardware.|

#Server Roles

When running the benchmark suite, we use three server roles.  These roles can be fulfilled by three distinct computers (physical hardware), three virtual machines, by a single computer acting in all three roles simultaneously, or any other blend.

The full benchmark requires at least three server roles:

* `app server`: The server that hosts each framework during a run, inclusive of web servers where applicable.
* `load server`: The server that generates client load during a run. Also known as the `client` or `client machine`.
* `database server`: The server that runs the databases used during a run. Also known as the `DB server`.

This codebase (`TechEmpower/FrameworkBenchmarks` aka `TFB`) must be run on 
the `app server`. The codebase contains a number of `framework directories`, each 
of which contains one or more `framework test implementations`. While our current setup has 
many directories, we are working to consolidate related code into fewer directories with more tests per directory.

When run, `TFB` will: 

* Select which framework tests are to be run based on command-line arguments you provide
* Install the necessary software (both on the `app server` and other servers)
* Launch the framework(s) you've requested, each in turn
* Launch the necessary database servers
* Access the URIs listed in [the requirements](http://www.techempower.com/benchmarks/#section=code) and verify the responses
* Launch the load generation software on the `load server`
* Gather the results
* Halt the framework, and repeat for the next framework as specified by command-line arguments
