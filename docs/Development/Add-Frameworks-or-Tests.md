When adding a new framework or new test to an existing framework, please follow these steps:

* Add an [install.sh file](Codebase/Framework-Files#install-file).
* Add a [setup file](Codebase/Framework-Files#setup-file).
* Add a [benchmark_config](Codebase/Framework-Files#benchmark-config-file).
* Implement at least one [test](Project-Information/Framework-Tests#test-types) 
(we'd be even happier with six completed tests). When creating a database test, 
use the table/collection `hello_world`. Our database setup scripts are stored 
inside the `config/` folder if you need to see the database schema.
* [Test your framework](Development/Testing-and-Debugging) appropriately.
* When all tests are passing, submit a pull request following the 
[pull request procedure](Development/Contributing-Guide#github-pull-request-procedure).
