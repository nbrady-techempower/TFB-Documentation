 ### Adding a New Test
 
 To create a new framework you can start the new test initialization wizard with
the command:
 
 ```
 tfb --new
 ```
 
 This will walk you through the scaffolding process. You will still need to
follow the steps in [Additional Information](#additional-information) below.
 
 ### Adding a New Test Without the Installation Wizard
 
 When adding a new framework or new test to an existing framework, please follow
these steps:
 
 * Add/Update a [test docker
file](../Codebase/Framework-Files#test-docker-file).
 * Add/Update a
[benchmark_config.json](../Codebase/Framework-Files#benchmark-config-file).
 * Implement at least one
[test](../Project-Information/Framework-Tests#test-types) 
 (we'd be even happier with six completed tests). When creating a database test,
 use the table/collection `hello_world`. Our database setup scripts are stored 
 inside the `config/` folder if you need to see the database schema.
 * Add/Update a [README](Readme-Formats#language-readmes) for your 
 framework.
 * Add/Update a [README](Readme-Formats#framework-readmes) for your
 language.
 
 ### Additional Information
 
 * Make sure that *any* packages/dependencies you are using or any git source
repositories you are pulling in are locked down to specific version numbers or
releases.
 * Add an entry for the framework test directory in `.travis.yml` if the
framework is new to the benchmarks.
 * [Test your framework](Testing-and-Debugging) appropriately.
     * Ensure the framework tests implemented pass in your local environment.
     * Travis CI will test the framework when a pull request is opened. _Tip:
Setup your own [Travis CI](https://travis-ci.org) account and know the outcome
of the tests before you submit a merge request. See [Travis CI Tips and
Tricks](../Project-Information/Travis-CI#tricks-and-tips-for-travis-ci) for
more details._
 * When all tests are passing, submit a pull request following the 
 [pull request procedure](Contributing-Guide#github-pull-request-procedure).
 
