# Testing

## Listing Tests

You can easily list all available tests

```bash
$ toolset/run-tests.py --list-tests
activeweb
activeweb-raw
aspnet
aspnet-jsonnet
aspnet-mongodb-raw
aspnet-mono
aspnet-mono-jsonnet
aspnet-mono-mongodb-raw
<snip>
```

## Running Tests

There are a number of options that can be specified: 

```bash
# Run a verification for test beego
$ toolset/run-tests.py --test beego --mode verify

# Run the default benchmark for the beego test
$ toolset/run-tests.py --test beego

# Specify which test types are run during benchmark
$ toolset/run-tests.py --test beego --type json
$ toolset/run-tests.py --test beego --type db
$ toolset/run-tests.py --test beego --type fortune

# Specify a number of options for how the load is generated
$ toolset/run-tests.py --test beego --max-concurrency 24 --max-threads 24 --duration 20 --max-queries 200

# Run a tiny benchmark
$ toolset/run-tests.py --test beego --max-threads 2 --max-concurrency 2 
```
_Note: The `results` directory must be removed after each test has been run 
in order to run the test again._

# Testing on both Windows and Linux

If your framework and platform can execute on both Windows and Linux, 
we encourage you to specify tests for both operating systems.  This 
increases the amount of testing you should do before submitting your 
pull-request, however, so we understand if you start with just one 
of the two. Travis-CI cannot automatically verify Windows-based 
tests, and therefore you should verify your code manually.

The steps involved are:

* Assuming you have implemented the Linux test already, add a new test 
permutation to your `benchmark_config.json` file for the Windows test.  When 
the benchmark script runs on Linux, it skips tests where `os` is 
`Windows` and vice versa.
* Add the necessary tweaks to your 
[setup file](../Codebase/Framework-Files#setup-file) to start and stop on 
the new operating system.  See, for example, 
[the script for HttpListener](https://github.com/TechEmpower/FrameworkBenchmarks/blob/master/frameworks/CSharp/HttpListener/setup.ps1).
* Test on Windows and Linux to make sure everything works as expected.

# Travis-CI
Enable [Travis-CI](https://travis-ci.org) on your project fork. This is 
as simple as going to travis-ci.org, using the `Log in with Github` 
button, and enabling Travis-CI for your fork. If you're submitting a new 
framework, be sure to add it to the `travis.yml` file in the root of the
project. When your development is done and your changes pass the Travis-CI 
verification, submit a pull request with confidence that it can be merged 
quickly. [Read more about TFB and Travis-CI](../Project-Information/Travis-CI.md).

### Important Note About Travis-CI

If you make changes to configuration files, or files outside your framework
directory, Travis will run tests on all existing frameworks. For this reason,
it may appear that your tests have failed. Be sure to check your Travis build
by clicking on the checkmark or red 'X' to dig into your specific test.

# Debugging

## Finding output logs

Logs file locations use the format `results/latest/logs/wt/err.txt`.
The general structure is `results/<run name>/<timestamp>/logs/<test name>/<file>`.
You can use the `--name` flag to change the `<run name>`.
If you re-run the same test multiple times, you will get a different folder
for each `<timestamp>`, although the `latest` folder will be kept up to date. 
The `<test name>` is simply the name of the test type you ran, and `<file>` is either `out.txt`
or `err.txt` (these are the `logout` and `logerr` arguments passed into each 
`setup.py` file. 

Note: If you're looking for logs from our official benchmark rounds, see 
[Round 13](http://tfb-logs.techempower.com/round-13/),
[Round 12](http://tfb-logs.techempower.com/round-12/), and
[Round 11](http://tfb-logs.techempower.com/round-11/)
