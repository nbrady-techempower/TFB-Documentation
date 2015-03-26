#Simulating Production Environments
For this project, we aimed to configure every framework according to the best practices for production deployments gleaned from documentation and popular community opinion. The goal is approximating a sensible production deployment as accurately as possible. We also want this project to be as transparent as possible, which is why we have posted our test suites on [GitHub](https://github.com/TechEmpower/FrameworkBenchmarks/).

#Environment Details

For language and framework specific details, view their README within their respective [directory](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/frameworks).

##Hardware
* [Peak](http://www.peakhosting.com/): Dell R720xd dual Xeon E5-2660 v2 servers with 32 GB memory; database servers equipped with SSDs in RAID; switched 10-gigabit Ethernet
* EC2: Amazon EC2 c3.large instances; switched gigabit Ethernet

##Operating Systems
* [Ubuntu Linux](http://www.ubuntu.com/desktop) 12.04 64-bit

##Databases
* [MySQL](http://dev.mysql.com/)
* [MongoDB](http://www.mongodb.org/)
* [PostrgeSQL](http://www.postgresql.org/)

##Load simulator
* [Wrk](https://github.com/wg/wrk)
