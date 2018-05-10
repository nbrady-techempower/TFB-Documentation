 We expect that you might have a bunch of questions. Here are some that we're
anticipating. But please contact us if you have a question we're not dealing
with here or just want to tell us we're doing it wrong.
 
 #Frameworks and configuration
 1. __"You call x a framework, but it's a platform."__ See the terminology
section above. We are using the word "framework" loosely to refer to anything
found on the spectrum ranging from full-stack frameworks, micro-frameworks, to
platforms. If it's used to build web applications, it probably qualifies. That
said, we understand that comparing a full-stack framework versus platforms or
vice-versa is unusual. We feel it's valuable to be able to compare these, for
example to understand the performance overhead of additional abstraction. You
can use the filters in the results viewer to adjust the rows you see in the
charts.
 2. __"You configured framework x incorrectly, and that explains the numbers
you're seeing."__ Whoops! Please let us know how we can fix it, or submit a
[GitHub](https://github.com/TechEmpower/FrameworkBenchmarks) pull request, so
we can get it right.
 3. __"Why include this Gemini framework I've never heard of?"__ We have
included our in-house Java web framework, Gemini, in our tests. We've done so
because it's of interest to us. You can consider it a stand-in for any
relatively lightweight minimal-locking Java framework. While we're proud of how
it performs among the well-established field, this exercise is not about
Gemini.
 4. __"Why don't you test framework X?"__ We'd love to, if we can find the time.
Even better, craft the test implementation yourself and submit a
[GitHub](https://github.com/TechEmpower/FrameworkBenchmarks) pull request so we
can get it in there faster!
 5. __"Some frameworks use process-level concurrency; have you accounted for
that?"__ Yes, we've attempted to use production-grade configuration settings
for all frameworks, including those that rely on process-level concurrency. For
the EC2 tests, for example, such frameworks are configured to utilize the two
virtual cores provided on an m1.large instance. For the i7 tests, they are
configured to use the eight hyper-threading cores of our hardware's i7 CPUs.
 6. __"Have you enabled [APC](http://php.net/manual/en/book.apc.php) for the PHP
tests?"__ Yes, the PHP tests run with APC and PHP-FPM on nginx.
 7. __"Why are you using a (slightly) old version of framework X?"__ It's
nothing personal! With so many frameworks we have a never-ending game of
whack-a-mole. If you think an update will affect the results, please let us
know (or better yet, submit a GitHub pull request) and we'll get it updated!
 8. __"It's unfair and possibly even incorrect to compare X and Y!"__ It may be
alarming at first to see the full results table, where one may evaluate
frameworks vs platforms; MySQL vs Postgres; Go vs Python; ORM vs raw database
connectivity; and any number of other possibly irrational comparisons. Many
readers desire the ability to compare these and other permutations. If you
prefer to view an unpolluted subset, you may use the filters available at the
top of the results page. We believe that comparing frameworks with plausible
and diverse technology stacks, despite the number of variables, is precisely
the value of this project. With sufficient time and effort, we hope to
continuously broaden the test permutations. But we recommend against ignoring
the data on the basis of concerns about multi-variable comparisons. Read more
opinion on this at [Brian Hauer's personal
blog](http://tiamat.tsotech.com/unfair-comparisons).
 9. __"If you are testing production deployments, why is logging disabled?"__ At
present, we have elected to run tests with logging features disabled. Although
this is not consistent with production deployments, we avoid a few
complications related to logging, most notably disk capacity and consistent
granularity of logging across all test implementations. In spot tests, we have
not observed significant performance impact from logging when enabled. If there
is strong community consensus that logging is necessary, we will reconsider
this.
 10. __"Tell me about the Windows configuration."__ We are very thankful to the
community members who have contributed Windows tests. In fact, nearly the
entirety of the Windows configuration has been contributed by subject-matter
experts from the community. Thanks to their effort, we now have tests covering
both Windows paired with Linux databases and Windows paired with Microsoft SQL
Server. As with all aspects of this project, we welcome continued input and
tuning by other experts. If you have advice on better tuning the Windows tests,
please submit [GitHub](https://github.com/TechEmpower/FrameworkBenchmarks)
issues or pull requests.
 
 #The tests
 11. __"Framework X has in-memory caching, why don't you use that?"__ In-memory
caching, as provided by some frameworks, yields higher performance than
repeatedly hitting a database, but isn't available in all frameworks, so we
omitted in-memory caching from these tests. Cache tests are planned for later
rounds.
 12. __"What about other caching approaches, then?"__ Remote-memory or
near-memory caching, as provided by Memcached and similar solutions, also
improves performance and we would like to conduct future tests simulating a
more expensive query operation versus Memcached. However, curiously, in spot
tests, some frameworks paired with Memcached were conspicuously slower than
other frameworks directly querying the authoritative MySQL database
(recognizing, of course, that MySQL had its entire data-set in its own memory
cache). For simple "get row ID n" and "get all rows" style fetches, a fast
framework paired with MySQL may be faster and easier to work with versus a slow
framework paired with Memcached.
 13. __"Why doesn't your test include more substantial algorithmic work?"__
Great suggestion. We hope to in the future!
 14. __"What about [reverse proxy](http://en.wikipedia.org/wiki/Reverse_proxy)
options such as Varnish?"__ We are expressly not using reverse proxies on this
project. There are other benchmark projects that evaluate the performance of
reverse proxy software. This project measures the performance of web
applications in any scenario where requests reach the application server. Given
that objective, allowing the web application to avoid doing the work thanks to
a reverse proxy would invalidate the results. If it's difficult to
conceptualize the value of measuring performance beyond the reverse proxy,
imagine a scenario where every response provides user-specific and varying
data. It's also notable that some platforms respond with sufficient performance
to potentially render a reverse proxy unnecessary.
 15. __"Do all the database tests use connection pooling?"__ Yes, our
expectation is that all tests use connection pooling.
 16. __"How is each test run?"__ Each test is executed as follows:
     1. Restart the database servers.
     2. Start the platform and framework using their start-up mechanisms.
     3. Run a 5-second __primer__ at 8 client-concurrency to verify that the
server is in fact running. These results are not captured.
     4. Run a 15-second __warmup__ at 256 client-concurrency to allow
lazy-initialization to execute and just-in-time compilation to run. These
results are not captured.
     5. Run a 15-second __captured test__ for each of the concurrency levels (or
iteration counts) exercised by the test type. Concurrency-variable test types
are tested at 8, 16, 32, 64, 128, and 256 client-side concurrency. The
high-concurrency plaintext test type is tested at 256, 1,024, 4,096, and 16,384
client-side concurrency.
     6. Stop the platform and framework.
 17. __"Hold on, 15 seconds is not enough to gather useful data."__ This is a
reasonable concern. But in examining the data, we have seen no evidence that
the results have changed by reducing the individual test durations from 60
seconds to 15 seconds. The duration reduction was made necessary by the growing
number of test permutations and a target that the full suite complete in a
reasonable amount of time (it currently takes 2-3 days for a full run with 15
second test duration).  We are currently building a continuously-running test
environment that will pull the latest source and begin a new run as soon as a
previous run completes. When the continuous benchmarking environment is ready,
we will be comfortable with longer execution times. At that point, we plan on
extending the duration of each test back up to 30 or possibly 60 seconds.
 18. __"Also, a 15-second warmup is not sufficient."__ On the contrary, we have
not yet seen evidence suggesting that any additional warmup time is beneficial
to any framework. In fact, for frameworks based on JIT platforms such as the
Java Virtual Machine (JVM), spot tests show that the JIT has even completed its
work already after just the primer and before the warmup starts—the warmup
(256-concurrency) and real 256-concurrency tests yield results that are
separated only by test noise. However, as with test durations, we intend to
increase the duration of the warmup when we have a continuously-running test
environment.
 
 #Environment
 
 19. __"What is Wrk?"__ Although many web performance tests use ApacheBench from
Apache to generate HTTP requests, we now use [Wrk](https://github.com/wg/wrk)
for this project. ApacheBench remains a single-threaded tool, meaning that for
higher-performance test scenarios, ApacheBench itself is a limiting factor. Wrk
is a multithreaded tool that provides a similar function, allowing tests to run
for a prescribed amount of time (rather than limited to a number of requests)
and providing us result data including total requests completed and latency
information.
 20. __"Doesn't benchmarking on a cloud platform like Amazon EC2 or Microsoft
Azure invalidate the results?"__ Our opinion is that doing so confirms
precisely what we're trying to test: performance of web applications within
realistic production environments. Testing on cloud platforms allows the tests
to be readily verified by anyone interested in doing so. However, we also
execute tests on higher-performance physical machines running Ubuntu as a
non-virtualized comparison. Doing so confirmed our suspicion that the ranked
order and relative performance across frameworks is mostly consistent between
cloud and physical environments. That is, while the EC2 and Azure instances
were slower than the physical hardware, they were slower by roughly the same
proportion across the spectrum of frameworks.
 21. __"Tell me about your physical hardware."__ You can view a detailed
breakdown of our hardware environments, as well as which rounds they were used
for, [on our results
site](https://www.techempower.com/benchmarks/#section=environment&hw=ph&test=fortune).
 22. __"What is Resin? Why aren't you using Tomcat for the Java frameworks?"__
Resin is a Java application server. The GPL version that we used for our tests
is a relatively lightweight Servlet container. We tested on Tomcat as well but
ultimately dropped Tomcat from our tests because Resin was slightly faster
across all Servlet-based frameworks.
 23. __"Do you run any warmups before collecting results data?"__ Yes. See "how
is each test run" above. Every test is preceded by a warmup and brief (several
seconds) cooldown prior to gathering test data.
 
 # Results
 
 24. __"I am about to start a new web application project; how should I
interpret these results?"__ Most importantly, recognize that performance data
should be one part of your decision-making process. High-performance web
applications reduce hosting costs and improve user experience. Additionally,
recognize that while we have aimed to select test types that represent
workloads that are common for web applications, nothing beats conducting
performance tests yourself for the specific workload of your application. In
addition to performance, consider other requirements such as your language and
platform preference; your invested knowledge in one or more of the frameworks
we've tested; and the documentation and support provided by the framework's
community. Combined with an examination of [the source
code](https://github.com/TechEmpower/FrameworkBenchmarks), the results seen
here should help you identify a platform and framework that is high-performance
while still meeting your other requirements.
 25. __"Why are the leaderboards for JSON Serialization and Plaintext so
different on Cloud versus Physical?"__ Put briefly, for fast frameworks on our
physical hardware, the limiting factor for the JSON test is the client
load-generating machine, which is less powerful than the machine running the
app server; whereas on cloud environments, the limit is the CPU. 
 26. __"Where did earlier rounds go?"__ To better capture HTTP errors reported
by Wrk, we have restructured the format of our results.json file. The test tool
changed at Round 2 and some framework IDs were changed at Round 3. As a result,
the results.json for Rounds 1 and 2 would have required manual editing and we
opted to simply remove the previous rounds from this site. You can still see
those rounds at our blog: [Round
1](https://www.techempower.com/blog/2013/03/28/frameworks-round-1/), [Round
2](https://www.techempower.com/blog/2013/04/05/frameworks-round-2/).
 27. __"What does 'Did not complete' mean?"__ Starting with Round 9, we have
added validation checks to confirm that implementations are behaving as we have
specified in [the requirements
section](/Project-Information/Framework-Tests#general-requirements). An
implementation that does not return the correct results, bypasses some of the
requirements, or even formats the results in a manner inconsistent with the
requirements will be marked as "Did not complete." We have solicited
corrections from prior contributors and have attempted to address many of
these, but it will take more time for all implementations to be correct. If you
are a project participant and your contribution is marked as "Did not
complete," please help us resolve this by contacting us at the [GitHub
repository](https://github.com/TechEmpower/FrameworkBenchmarks). We may
ultimately need a pull request from you, but we'd be happy to help you
understand what specifically is triggering a validation error with your
implementation.
 
