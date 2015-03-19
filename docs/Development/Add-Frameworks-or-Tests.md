When adding a new framework or new test to an existing framework, please follow 
these steps:

* Add/Update an [install.sh file](Codebase/Framework-Files#install-file).
* Add/Update a [setup file](Codebase/Framework-Files#setup-file).
* Add/Update a [benchmark_config](Codebase/Framework-Files#benchmark-config-file).
* Implement at least one [test](Project-Information/Framework-Tests#test-types) 
(we'd be even happier with six completed tests). When creating a database test, 
use the table/collection `hello_world`. Our database setup scripts are stored 
inside the `config/` folder if you need to see the database schema.
* [Test your framework](Development/Testing-and-Debugging) appropriately.
* Add/Update a [README](Development/Readme-Formats#language-readmes) for your 
framework.
* Add/Update a [README](Development/Readme-Formats#framework-readmes) for your
language.
* When all tests are passing, submit a pull request following the 
[pull request procedure](Development/Contributing-Guide#github-pull-request-procedure).
