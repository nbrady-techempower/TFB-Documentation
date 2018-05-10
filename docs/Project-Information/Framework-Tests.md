 We invite fans of frameworks, and especially authors or maintainers of
frameworks, to join us in expanding the coverage of this project by
implementing tests and contributing to the GitHub repository. The following are
specifications for each of the test types we have included to-date in this
project. Do not be alarmed; the specifications read quite verbose, but that's
because they are specifications. The implementations tend to be quite easy in
practice.
 
 This project is evolving and we will periodically add new test types. As new
test types are added, we encourage but do not require contributors of previous
implementations to implement tests for the new test types. Wholly new test
implementations are also encouraged to include all test types but are not
required to do so. If you have limited time, we recommend you start with the
easiest test types (1, 2, 3, and 6) and then continue beyond those as time
permits.
 
 #Test Types
 
 Each test type has their own requirements and specifications. Visit their
sections for more details and complete requirements.
 
 1. [__JSON Serialization__](#json-serialization): Exercises the framework
fundamentals including keep-alive support, request routing, request header
parsing, object instantiation, JSON serialization, response header generation,
and request count throughput.
 2. [__Single Database Query__](#single-database-query): Exercises the
framework's object-relational mapper (ORM), random number generator, database
driver, and database connection pool.
 3. [__Multiple Database Queries__](#multiple-database-queries): A variation of
[Test #2](#Single-Database-Query) and also uses the __World__ table. Multiple
rows are fetched to more dramatically punish the database driver and connection
pool. At the highest queries-per-request tested (20), this test demonstrates
all frameworks' convergence toward zero requests-per-second as database
activity increases.
 4. [__Fortunes__](#fortunes): Exercises the ORM, database connectivity,
dynamic-size collections, sorting, server-side templates, XSS countermeasures,
and character encoding.
 5. [__Database Updates__](#database-updates): A variation of [Test
#3](#multiple-database-queries) that exercises the ORM's persistence of objects
and the database driver's performance at running `UPDATE` statements or
similar. The spirit of this test is to exercise a variable number of
read-then-write style database operations.
 6. [__Plaintext__](#plaintext): An exercise of the request-routing fundamentals
only, designed to demonstrate the capacity of high-performance platforms in
particular. Requests will be sent using HTTP pipelining. The response payload
is still small, meaning good performance is still necessary in order to
saturate the gigabit Ethernet of the test environment.
 7. [__Caching__](#caching): Exercises the platform or framework's in-memory
caching of information sourced from a database.  For implementation simplicity,
the requirements are very similar to the multiple database query test ([Test
#3](#multiple-database-queries)), but use a separate database table and are
fairly generous/forgiving, allowing for each platform or framework's best
practices to be applied.
 
 #General Test Requirements
 
 The following requirements apply to all test types below.
 
 1. All test implementations __should be production-grade__. The particulars of
this will vary by framework and platform, but the general sentiment is that the
code and configuration should be suitable for a production deployment. The word
should is used here because production-grade is our goal, but we don't want
this to be a roadblock. If you're submitting a new test and uncertain whether
your code is production-grade, submit it anyway and then solicit input from
other subject-matter experts.
 2. All test implementations __must disable all disk logging__. For many
reasons, we expect all tests will run without writing logs to disk. Most
importantly, the volume of requests is sufficiently high to fill up disks even
with only a single line written to disk per request. Please disable all forms
of disk logging. We recommend but do not require disabling console logging as
well.
 3. Specific characters and character case matter. Assume the client consuming
your service's JSON responses will be using a case-sensitive language such as
JavaScript. In other words, if a test specifies that a map's key is id, use id.
Do not use Id or ID. This strictness is required not only because it's sensible
but also because our automated validation checks are picky.
 
 #Specific Test Requirements
 
 1. ##JSON Serialization
 
     The __JSON Serialization__ test exercises the framework fundamentals
including keep-alive support, request routing, request header parsing, object
instantiation, JSON serialization, response header generation, and request
count throughput.
 
     ###Requirements
 
     In addition to the requirements listed below, please note the [general
requirements](#general-test-requirements) that apply to all implemented tests.
 
     1. For each request, an object mapping the key `message` to `Hello, World!`
must be instantiated.
     2. The recommended URI is __/json__.
     3. A JSON serializer must be used to convert the object to JSON.
     4. The response text must be `{"message":"Hello, World!"}`, but white-space
variations are acceptable.
     5. The response content length should be approximately 28 bytes.
     6. The response content type must be set to `application/json`.
     7. The response headers must include either `Content-Length` or
`Transfer-Encoding`.
     8. The response headers must include `Server` and `Date`.
     9. gzip compression is not permitted.
     10. Server support for HTTP Keep-Alive is strongly encouraged but not
required.
     11. If HTTP Keep-Alive is enabled, no maximum Keep-Alive timeout is
specified by   this test.
     12. The request handler will be exercised at concurrency levels ranging
from 8 to 256.
     13. The request handler will be exercised using GET requests.
 
     ###Example request
 
         GET /json HTTP/1.1
         Host: server
         User-Agent: Mozilla/5.0 (X11; Linux x86_64) Gecko/20130501 Firefox/30.0
AppleWebKit/600.00 Chrome/30.0.0000.0 Trident/10.0 Safari/600.00
         Cookie: uid=12345678901234567890;
__utma=1.1234567890.1234567890.1234567890.1234567890.12; wd=2560x1600
         Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
         Accept-Language: en-US,en;q=0.5
         Connection: keep-alive
 
     ###Example response
 
         HTTP/1.1 200 OK
         Content-Type: application/json; charset=UTF-8
         Content-Length: 28
         Server: Example
         Date: Wed, 17 Apr 2013 12:00:00 GMT
         
         {"message":"Hello, World!"}
 
 2. ##Single Database Query
 
     The __Single Database Query__ test exercises the framework's
object-relational mapper (ORM), random number generator, database driver, and
database connection pool.
 
     ###Requirements
 
     In addition to the requirements listed below, please note the [general
requirements](#general-test-requirements) that apply to all implemented tests.
 
     1. For every request, a single row from a __World__ table must be retrieved
from a database table.
     2. The recommended URI is __/db__.
     3. The schema for __World__ is __id__ (int, primary key) and
__randomNumber__ (int), except for MongoDB, wherein the identity column is
<strong>_id</strong>, with the leading underscore. For redis, use `GET
"world:$id"` to fetch the value.
     4. The __World__ table is known to contain 10,000 rows.
     5. The row retrieved must be selected by its id using a random number
generator (ids range from 1 to 10,000).
     6. The row should be converted to an object using an object-relational
mapping (ORM) tool. Tests that do not use an ORM will be classified as "raw"
meaning they use the platform's raw database connectivity.
     7. The object (or database row, if an ORM is not used) must be serialized
to JSON.
     8. The response content length should be approximately 32 bytes.
     9. The response content type must be set to `application/json`.
     10. The response headers must include either Content-Length or
Transfer-Encoding.
     11. The response headers must include `Server` and `Date`.
     12. Use of an in-memory cache of __World__ objects or rows by the
application is not permitted.
     13. Use of prepared statements for SQL database tests (e.g., for MySQL) is
encouraged but not required.
     14. gzip compression is not permitted.
     15. Server support for HTTP Keep-Alive is strongly encouraged but not
required.
     16. If HTTP Keep-Alive is enabled, no maximum Keep-Alive timeout is
specified by this test.
     17. The request handler will be exercised at concurrency levels ranging
from 8 to 256.
     18. The request handler will be exercised using GET requests.
 
     ###Example request
     
         GET /db HTTP/1.1
         Host: server
         User-Agent: Mozilla/5.0 (X11; Linux x86_64) Gecko/20130501 Firefox/30.0
AppleWebKit/600.00 Chrome/30.0.0000.0 Trident/10.0 Safari/600.00
         Cookie: uid=12345678901234567890;
__utma=1.1234567890.1234567890.1234567890.1234567890.12; wd=2560x1600
         Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
         Accept-Language: en-US,en;q=0.5
         Connection: keep-alive
 
     ###Example response
 
         HTTP/1.1 200 OK
         Content-Length: 32
         Content-Type: application/json; charset=UTF-8
         Server: Example
         Date: Wed, 17 Apr 2013 12:00:00 GMT
         
         {"id":3217,"randomNumber":2149}
 
 3. ##Multiple Database Queries 
 
     The __Multiple Database Queries__ test is a variation of [Test
#2](#single-database-query) and also uses the __World__ table. Multiple rows
are fetched to more dramatically punish the database driver and connection
pool. At the highest queries-per-request tested (20), this test demonstrates
all frameworks' convergence toward zero requests-per-second as database
activity increases.
 
     ###Requirements
 
     In addition to the requirements listed below, please note the [general
requirements](#general-test-requirements) that apply to all implemented tests.
 
     1. For every request, an integer query string parameter named `queries`
must be retrieved from the request. The parameter specifies the number of
database queries to execute in preparing the HTTP response (see below).
     2. The recommended URI is __/queries__. _Note: Whether there is an actual
request parameter named `queries` is not important. The url should best reflect
the practices of the particular framework. So, both "/queries?queries=" and
"/queries/" are valid paths._
     3. The `queries` parameter must be bounded to between 1 and 500. If the
parameter is missing, is not an integer, or is an integer less than 1, the
value should be interpreted as 1; if greater than 500, the value should be
interpreted as __500__.
     4. The request handler must retrieve a set of __World__ objects, equal in
count to the `queries` parameter, from the __World__ database table.
     5. Each row must be selected randomly in the same fashion as the single
database query test (Test #2 above).
     6. This test is designed to exercise multiple queries, each requiring a
round-trip to the database server, and with each resulting row selected
individually. It is not acceptable to use batches. It is not acceptable to
execute multiple SELECTs within a single statement. It is not acceptable to
retrieve all required rows using a `SELECT ... WHERE id IN (...)` clause.
     7. Each __World__ object must be added to a list or array.
     8. The list or array must be serialized to JSON and sent as a response.
     9. The response content type must be set to `application/json`.
     10. The response headers must include either `Content-Length` or
`Transfer-Encoding`.
     11. The response headers must include `Server` and `Date`.
     12. Use of an in-memory cache of __World__ objects or rows by the
application is not permitted.
     13. Use of prepared statements for SQL database tests (e.g., for MySQL) is
encouraged but not required.
     14. gzip compression is not permitted.
     15. Server support for HTTP Keep-Alive is strongly encouraged but not
required.
     16. If HTTP Keep-Alive is enabled, no maximum Keep-Alive timeout is
specified by this test.
     17. The request handler will be exercised at 256 concurrency only.
     18. The request handler will be exercised with query counts of 1, 5, 10,
15, and 20.
     19. The request handler will be exercised using GET requests.
 
     ###Example request
 
         GET /queries?queries=10 HTTP/1.1
         Host: server
         User-Agent: Mozilla/5.0 (X11; Linux x86_64) Gecko/20130501 Firefox/30.0
AppleWebKit/600.00 Chrome/30.0.0000.0 Trident/10.0 Safari/600.00
         Cookie: uid=12345678901234567890;
__utma=1.1234567890.1234567890.1234567890.1234567890.12; wd=2560x1600
         Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
         Accept-Language: en-US,en;q=0.5
         Connection: keep-alive
 
     ###Example response
 
         HTTP/1.1 200 OK
         Content-Length: 315
         Content-Type: application/json; charset=UTF-8
         Server: Example
         Date: Wed, 17 Apr 2013 12:00:00 GMT
         
        
[{"id":4174,"randomNumber":331},{"id":51,"randomNumber":6544},{"id":4462,"randomNumber":952},{"id":2221,"randomNumber":532},{"id":9276,"randomNumber":3097},{"id":3056,"randomNumber":7293},{"id":6964,"randomNumber":620},{"id":675,"randomNumber":6601},{"id":8414,"randomNumber":6569},{"id":2753,"randomNumber":4065}]
 
 4. ##Fortunes
 
     The __Fortunes__ test exercises the ORM, database connectivity,
dynamic-size collections, sorting, server-side templates, XSS countermeasures,
and character encoding.
 
     ###Requirements
 
     In addition to the requirements listed below, please note the [general
requirements](#general-test-requirements) that apply to all implemented tests.
 
     1. The recommended URI is __/fortunes__.
     2. A __Fortune__ database table contains a dozen Unix-style fortune-cookie
messages.
     3. The schema for __Fortune__ is __id__ (int, primary key) and __message__
(varchar), except for MongoDB, wherein the identity column is
<strong>_id</strong>, with the leading underscore. For redis, use `LRANGE
"fortunes", 0, -1` to fetch the values.
     4. Using an ORM, all Fortune objects must be fetched from the Fortune
table, and placed into a list data structure. Tests that do not use an ORM will
be classified as "raw" meaning they use the platform's raw database
connectivity.
     5. The list data structure must be a dynamic-size or equivalent and should
not be dimensioned using foreknowledge of the row-count of the database table.
     6. Within the scope of the request, a new Fortune object must be
constructed and added to the list. This confirms that the data structure is
dynamic-sized. The new fortune is not persisted to the database; it is
ephemeral for the scope of the request.
     7. The new Fortune's message must be __"Additional fortune added at request
time."__
     8. The list of Fortune objects must be sorted by the order of the `message`
field. No `ORDER BY` clause is permitted in the database query (ordering within
the query would be of negligible value anyway since a newly instantiated
Fortune is added to the list prior to sorting).
     9. The sorted list must be provided to a server-side template and rendered
to simple HTML (see below for minimum template). The resulting HTML table
displays each Fortune's `id` number and `message` text.
     10. This test does not include external assets (CSS, JavaScript); a later
test type will include assets.
     11. The HTML generated by the template must be sent as a response.
     12. Be aware that the `message` text fields are stored as UTF-8 and one of
the fortune cookie messages is in Japanese.
     13. The resulting HTML must be delivered using UTF-8 encoding.
     14. The Japanese fortune cookie message must be displayed correctly.
     15. Be aware that at least one of the `message` text fields includes a
`<script>` tag.
     16. The server-side template must assume the `message` text cannot be
trusted and must escape the message text properly.
     17. The implementation is encouraged to use best practices for templates
such as layout inheritence, separate header and footer files, and so on.
However, this is not required. We request that implementations do not manage
assets (JavaScript, CSS, images). We are deferring asset management until we
can craft a more suitable test.
     18. The response content type must be set to `text/html`.
     19. The response headers must include either `Content-Length` or
`Transfer-Encoding`.
     20. The response headers must include `Server` and `Date`.
     21. Use of an in-memory cache of Fortune objects or rows by the application
is not permitted.
     22. Use of prepared statements for SQL database tests (e.g., for MySQL) is
encouraged but not required.
     23. gzip compression is not permitted.
     24. Server support for HTTP Keep-Alive is strongly encouraged but not
required.
     25. If HTTP Keep-Alive is enabled, no maximum Keep-Alive timeout is
specified by this test.
     26. The request handler will be exercised at concurrency levels ranging
from 8 to 256.
     27. The request handler will be exercised using GET requests.
 
     ###Example request
 
         GET /fortunes HTTP/1.1
         Host: server
         User-Agent: Mozilla/5.0 (X11; Linux x86_64) Gecko/20130501 Firefox/30.0
AppleWebKit/600.00 Chrome/30.0.0000.0 Trident/10.0 Safari/600.00
         Cookie: uid=12345678901234567890;
__utma=1.1234567890.1234567890.1234567890.1234567890.12; wd=2560x1600
         Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
         Accept-Language: en-US,en;q=0.5    
         Connection: keep-alive
 
     ###Example response
 
         HTTP/1.1 200 OK
         Content-Length: 1196
         Content-Type: text/html; charset=UTF-8
         Server: Example
         Date: Wed, 17 Apr 2013 12:00:00 GMT
 
         <!DOCTYPE
html><html><head><title>Fortunes</title></head><body><table><tr><th>id</th><th>message</th></tr><tr><td>11</td><td>&lt;script&gt;alert(&quot;This
should not be displayed in a browser alert
box.&quot;);&lt;/script&gt;</td></tr><tr><td>4</td><td>A bad random number
generator: 1, 1, 1, 1, 1, 4.33e+67, 1, 1, 1</td></tr><tr><td>5</td><td>A
computer program does what you tell it to do, not what you want it to
do.</td></tr><tr><td>2</td><td>A computer scientist is someone who fixes things
that aren&apos;t broken.</td></tr><tr><td>8</td><td>A list is only as strong as
its weakest link. — Donald Knuth</td></tr><tr><td>0</td><td>Additional fortune
added at request time.</td></tr><tr><td>3</td><td>After enough decimal places,
nobody gives a damn.</td></tr><tr><td>7</td><td>Any program that runs right is
obsolete.</td></tr><tr><td>10</td><td>Computers make very fast, very accurate
mistakes.</td></tr><tr><td>6</td><td>Emacs is a nice operating system, but I
prefer UNIX. — Tom Christaensen</td></tr><tr><td>9</td><td>Feature: A bug with
seniority.</td></tr><tr><td>1</td><td>fortune: No such file or
directory</td></tr><tr><td>12</td><td>??????????????</td></tr></table></body></html>
 
     ###Minimum template
 
     Along with the example response above, the following
[Mustache](http://mustache.github.io/) template illustrates the minimum
requirements for the server-side template. White-space can be optionally
eliminated.
 
         !<!DOCTYPE html>
         <html>
         <head><title>Fortunes</title></head>
         <body>
         <table>
         <tr><th>id</th><th>message</th></tr>
         {{#.}}
         <tr><td>{{id}}</td><td>{{message}}</td></tr>
         {{/.}}
         </table>
         </body>
         </html>
 
 5. ##Database Updates
 
     The __Database Updates__ test is a variation of [Test
#3](#multiple-database-queries) that exercises the ORM's persistence of objects
and the database driver's performance at running `UPDATE` statements or
similar. The spirit of this test is to exercise a variable number of
read-then-write style database operations.
 
     ###Requirements
 
     In addition to the requirements listed below, please note the [general
requirements](#general-test-requirements) that apply to all implemented tests.
 
     1. The recommended URI is __/updates__.
     2. For every request, an integer `query` string parameter named queries
must be retrieved from the request. The parameter specifies the number of rows
to fetch and update in preparing the HTTP response (see below).
     3. The `queries` parameter must be bounded to between 1 and 500. If the
parameter is missing, is not an integer, or is an integer less than __1__, the
value should be interpreted as 1; if greater than 500, the value should be
interpreted as __500__.
     4. The request handler must retrieve a set of __World__ objects, equal in
count to the `queries` parameter, from the __World__ database table.
     5. Each row must be selected randomly using one query in the same fashion
as the single database query test (Test #2 above). As with the read-only
multiple-query test type (#3 above), use of `IN` clauses or similar means to
consolidate multiple queries into one operation is not permitted.  Similarly,
use of a batch or multiple SELECTs within a single statement are not permitted.
     6. At least the `randomNumber` field must be read from the database result
set.
     7. Each __World__ object must have its `randomNumber` field updated to a
new random integer between 1 and 10000.
     8. Each __World__ object must be persisted to the database with its new
`randomNumber` value.
     9. Use of batch updates is acceptable but not required.  To be clear:
batches are not permissible for selecting/reading the rows, but batches are
acceptable for writing the updates.
     10. Use of transactions is acceptable.
     11. All updates should be executed prior to generating the HTTP response.
     12. For raw tests (that is, tests without an ORM), each updated row must
receive a unique new `randomNumber` value. It is not acceptable to change the
`randomNumber` value of all rows to the same random number using an `UPDATE ...
WHERE id IN (...)` clause.
     13. Each __World__ object must be added to a list or array.
     14. The list or array must be serialized to JSON and sent as a response.
     15. The response content type must be set to `application/json`.
     16. The response headers must include either `Content-Length` or
`Transfer-Encoding`.
     17. The response headers must include `Server` and `Date`.
     18. Use of an in-memory cache of __World__ objects or rows by the
application is not permitted.
     19. Use of prepared statements for SQL database tests (e.g., for MySQL) is
encouraged but not required.
     20. gzip compression is not permitted.
     21. Server support for HTTP Keep-Alive is strongly encouraged but not
required.
     22. If HTTP Keep-Alive is enabled, no maximum Keep-Alive timeout is
specified by this test.
     23. The request handler will be exercised at 256 concurrency only.
     24. The request handler will be exercised with query counts of 1, 5, 10,
15, and 20.
     25. The request handler will be exercised using GET requests.
 
     ###Example request
 
         GET /updates?queries=10 HTTP/1.1
         Host: server
         User-Agent: Mozilla/5.0 (X11; Linux x86_64) Gecko/20130501 Firefox/30.0
AppleWebKit/600.00 Chrome/30.0.0000.0 Trident/10.0 Safari/600.00
         Cookie: uid=12345678901234567890;
__utma=1.1234567890.1234567890.1234567890.1234567890.12; wd=2560x1600
         Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
         Accept-Language: en-US,en;q=0.5
         Connection: keep-alive
 
     ###Example response
 
         HTTP/1.1 200 OK
         Content-Length: 315
         Content-Type: application/json; charset=UTF-8
         Server: Example
         Date: Wed, 17 Apr 2013 12:00:00 GMT
 
        
[{"id":4174,"randomNumber":331},{"id":51,"randomNumber":6544},{"id":4462,"randomNumber":952},{"id":2221,"randomNumber":532},{"id":9276,"randomNumber":3097},{"id":3056,"randomNumber":7293},{"id":6964,"randomNumber":620},{"id":675,"randomNumber":6601},{"id":8414,"randomNumber":6569},{"id":2753,"randomNumber":4065}]
 
 6. ## Plaintext
 
     The __Plaintext__ test is an exercise of the request-routing fundamentals
only, designed to demonstrate the capacity of high-performance platforms in
particular. Requests will be sent using HTTP pipelining. The response payload
is still small, meaning good performance is still necessary in order to
saturate the gigabit Ethernet of the test environment.
 
     ###Requirements
 
     In addition to the requirements listed below, please note the [general
requirements](#general-test-requirements) that apply to all implemented tests.
 
     1. The recommended URI is __/plaintext__.
     2. The response content type must be set to `text/plain`.
     3. The response body must be `Hello, World!`.
     4. This test is not intended to exercise the allocation of memory or
instantiation of objects. Therefore it is acceptable but not required to re-use
a single buffer for the response text (`Hello, World`). However, the rest of
the response must be fully composed on the spot. It is not acceptable to store
the entire payload of the response, headers inclusive, as a pre-rendered
buffer.
     5. The response headers must include either `Content-Length` or
`Transfer-Encoding`.
     6. The response headers must include `Server` and `Date`.
     7. gzip compression is not permitted.
     8. Server support for HTTP Keep-Alive is strongly encouraged but not
required.
     9. Server support for HTTP/1.1 pipelining [is
assumed](http://www-archive.mozilla.org/projects/netlib/http/pipelining-faq.html).
Servers that do not support pipelining may be included but should downgrade
gracefully. If you are unsure about your server's behavior with pipelining,
test with the [wrk](https://github.com/wg/wrk) load generation tool used in our
tests.
     10. If HTTP Keep-Alive is enabled, no maximum Keep-Alive timeout is
specified by this test.
     11. The request handler will be exercised at 256, 1024, 4096, and 16,384
concurrency.
     12. The request handler will be exercised using GET requests.
 
     ###Example request
 
         GET /plaintext HTTP/1.1
         Host: server
         User-Agent: Mozilla/5.0 (X11; Linux x86_64) Gecko/20130501 Firefox/30.0
AppleWebKit/600.00 Chrome/30.0.0000.0 Trident/10.0 Safari/600.00
         Cookie: uid=12345678901234567890;
__utma=1.1234567890.1234567890.1234567890.1234567890.12; wd=2560x1600
         Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
         Accept-Language: en-US,en;q=0.5
         Connection: keep-alive
 
     ###Example response
 
         HTTP/1.1 200 OK
         Content-Length: 15
         Content-Type: text/plain; charset=UTF-8
         Server: Example
         Date: Wed, 17 Apr 2013 12:00:00 GMT
         
         Hello, World!
 
 7. ##Caching (Work in progress)
 
     The __Caching__ test exercises the preferred in-memory or separate-process
caching technology for the platform or framework.  For implementation
simplicity, the requirements are very similar to the multiple database-query
test [Test #3](#multiple-database-queries), but use a separate database table. 
The requirements are quite generous, affording each framework fairly broad
freedom to meet the requirements in the manner that best represents the
canonical non-distributed caching approach for the framework.  (Note: a
distributed caching test type could be added later.)
     
     This test type is a work-in-progress and may evolve in ways that break
implementations.  Early implementations are acceptable but not yet encouraged.
 
     ###Requirements
 
     In addition to the requirements listed below, please note the [general
requirements](#general-test-requirements) that apply to all implemented tests. 
Also see [Test #3](#multiple-database-queries) since this test type mimics that
quite closely, and has identical inputs and outputs from the perspective of the
HTTP client.
 
     1. For every request, an integer query string parameter named `count` must
be retrieved from the request. The parameter specifies the number of cached
objects to retrieve in preparing the HTTP response (see below).
     2. The recommended URI is __/cached-worlds__. _Note: Whether there is an
actual request parameter named `count` is not important. The url should best
reflect the practices of the particular framework. So, both
"/cached-worlds?count=" and "/cached-worlds/" are valid paths._
     3. The `count` parameter must be bounded to between 1 and 500. If the
parameter is missing, is not an integer, or is an integer less than 1, the
value should be interpreted as 1; if greater than 500, the value should be
interpreted as __500__.
     4. The request handler must retrieve a set of __CachedWorld__ objects,
equal in count to the `count` parameter, from the __CachedWorld__ database
table.  Note that the __CachedWorld__ database table uses a schema idential to
the __World__ table used for [Test #3](#multiple-database-queries) but is
distinct to avoid confusion.
     5. Each object must be selected randomly from the available pool of
__CachedWorld__ objects.  As with [Test #2](#single-database-query), it is
acceptable for implementations to leverage the knowledge that the identities of
these objects span from 1 to 10,000.
     6. Unlike [Test #3](#multiple-database-queries), this test type has no
requirement that implementations make requests to an external service. 
Additionally, if a separate caching process is used (see below), it is
_acceptable_ to fetch all of the randomly-selected objects using a single cache
query operation.
     7. The implementation may use any caching library or approach desired, with
the following caveats.
        a. Reverse-proxy caching is not permitted. This project is expressly not
a test of reverse-proxy caches (such as Varnish or nginx caching).
        b. The implementation should use a bona-fide cache library or server
with features such as read-through, a replacement algorithm, and so on.
Implementations should not create a plain key-value map of objects. If the
platform or framework does not ship with a canonical caching capability, use a
caching library or separate-process caching server that is realistic for a
production environment. A "reference implementation" would use Google's Guava
CacheBuilder for in-VM or Redis for separate-process caching.
        c. The implementation may use an in-process memory cache, a
separate-process memory cache, or any other conceivable approach (within
reasonable limits, of course).  As feasible, use the approach that is
considered canonical for the framework.
     8. The implementation must not require an additional server instance or
additional hardware. The cache should run alongside the application server
(either in-process or separate process on localhost).
     9. The application server must communicate with the cache; the
implementation should not expect that the HTTP client communicate with the
cache directly.
     10. As with other test types, a warm-up will occur prior to capturing
performance data. However, it is also acceptable for an implementations to
prime its cache during setup in whatever form needed, assuming that priming
process completes in a reasonable amount of time.
     7. Each __CachedWorld__ object retrieved from the cache must be added to a
list or array.
     8. The list or array must be serialized to JSON and sent as a response.
     9. The response content type must be set to `application/json`.
     10. The response headers must include either `Content-Length` or
`Transfer-Encoding`.
     11. The response headers must include `Server` and `Date`.
     14. gzip compression is not permitted.
     15. Server support for HTTP Keep-Alive is strongly encouraged but not
required.
     16. If HTTP Keep-Alive is enabled, no maximum Keep-Alive timeout is
specified by this test.
     17. The request handler will be exercised at 256 concurrency only.
     18. The request handler will be exercised with `counts` of 1, 10, 20, 50,
and 100.  Note that these are higher than the counts used in [Test
#3](#multiple-database-queries).
     19. The request handler will be exercised using GET requests.
 
     ###Example request
 
         GET /cached-worlds?count=10 HTTP/1.1
         Host: server
         User-Agent: Mozilla/5.0 (X11; Linux x86_64) Gecko/20130501 Firefox/30.0
AppleWebKit/600.00 Chrome/30.0.0000.0 Trident/10.0 Safari/600.00
         Cookie: uid=12345678901234567890;
__utma=1.1234567890.1234567890.1234567890.1234567890.12; wd=2560x1600
         Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
         Accept-Language: en-US,en;q=0.5
         Connection: keep-alive
 
     ###Example response
 
         HTTP/1.1 200 OK
         Content-Length: 315
         Content-Type: application/json; charset=UTF-8
         Server: Example
         Date: Wed, 17 Apr 2013 12:00:00 GMT
         
        
[{"id":4174,"randomNumber":331},{"id":51,"randomNumber":6544},{"id":4462,"randomNumber":952},{"id":2221,"randomNumber":532},{"id":9276,"randomNumber":3097},{"id":3056,"randomNumber":7293},{"id":6964,"randomNumber":620},{"id":675,"randomNumber":6601},{"id":8414,"randomNumber":6569},{"id":2753,"randomNumber":4065}]
 
