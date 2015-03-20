#Terminology

|Term|Description|
| --:|:--------- |
|framework|We use the word framework loosely to refer to any HTTP stack—a full-stack framework, a micro-framework, or even a web platform such as Rack, Servlet, or plain PHP.|
|permutation| A combination of attributes that compose a full technology stack being tested (such as node.js paired with MongoDB or node.js paired with MySQL). Some frameworks have seen many permutations contributed by the community; others only one or few.|
|test type|One of the workloads we exercise, such as JSON serialization, single-query, multiple-query, fortunes, data updates, and plaintext.|
|test|An individual test is a measurement of the performance of a permutation's implementation of a test type. For example, a test might be measuring Wicket paired with MySQL running the single-query test type.|
|implementation|Sometimes called "test implementations," these are the bodies of code and configuration created to test permutations according to the requirements. These are frequently contributed by the developer community. Basically, together with the toolset, test implementations are the meat of this project.|
|toolset|A set of Python scripts that run our tests.|
|run|An execution of the benchmark toolset across the suite of test implementations, either in full or in part, in order to capture results for any purpose.|
|preview|A capture of data from a run used by project participants to sanity-check prior to an official round.|
|round|A posting of "official" results on this web site. This is mostly for ease of consumption by readers and good-spirited & healthy competitive bragging rights. For in-depth analysis, we encourage you to examine the source code and run the tests on your own hardware.|

#Servers

The full benchmark requires at least three computers:

* `app server`: The computer that your framework will be launched on.
* `load server`: The computer that will generate client load. Also known as the `client machine`.
* `database server`: The computer that runs all the databases. Also known as the `DB server`.

This codebase (`TechEmpower/FrameworkBenchmarks` aka `TFB`) must be run on 
the `app server`. The codebase contains a number of `framework directories`, each 
of which contains one or more `framework test implementations`. While our current setup has 
many directories, we are working to consolidate related code into fewer directories  
with more tests per directory. 

When run, `TFB` will: 

* select which framework tests are to be run based on command-line arguments you provide
* install the necessary software (both on the `app server` and other servers)
* launch the framework
* access the urls listed in [the requirements](http://www.techempower.com/benchmarks/#section=code) and verify the responses
* launch the load generation software on the `load server`
* gather the results
* halt the framework
