#Simulating Production Environments
For this project, we aimed to configure every framework according to the best practices for production deployments gleaned from documentation and popular community opinion. The goal is approximating a sensible production deployment as accurately as possible. We also want this project to be as transparent as possible, which is why we have posted our test suites on [GitHub](https://github.com/TechEmpower/FrameworkBenchmarks/).

#Environment Details

For language and framework specific details, view their README within their respective [directory](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/frameworks).

##Hardware

### _[ServerCentral](https://www.servercentral.com/)_
  * Switched 10-gigabit ethernet

  #### TFB-server (App server)
  * 80 Cores Intel(R) Xeon(R) CPU E7-4850  @ 2.00GHz
  * 512gb RAM

  #### TFB-database
  * 16 Cores Intel(R) Xeon(R) CPU E5520  @ 2.27GHz
  * 16gb RAM
  
  #### TFB-client (wrk machine)
  * 8 Cores Intel(R) Xeon(R) CPU E5-2407 0 @ 2.20GHz
  * 32gb RAM
  
##Operating Systems
* [Ubuntu Linux](http://www.ubuntu.com/desktop) 14.04 64-bit

##Databases
* [MySQL](http://dev.mysql.com/)
* [MongoDB](http://www.mongodb.org/)
* [PostrgeSQL](http://www.postgresql.org/)

##Load simulator
* [Wrk](https://github.com/wg/wrk)
