# What is the Results Website?

The results website is located at [www.techempower.com/benchmarks](https://www.techempower.com/benchmarks). 
The results are gathered in rounds and then displayed on the results 
website for comparison.

You can also find the results of our continuous benchmarking updated live at [tfb-status.techempower.com](https://tfb-status.techempower.com). 

# FAQ

__1. What is the purpose of the column named "Errors" on the results 
website? (example is referencing round 8 results)__

The very briefest answer is that the Errors column reports sum of the total number of 500-series responses and socket connection, read, or write errors reported by Wrk during the peak test.
 
500-series responses are fairly straight-forward. For example, for wsgi-nginx-uwsgi, the peak plaintext performance was at 256 concurrency. Wrk reports that the server responded with non-200/300 responses (which we interpret as "500" responses) a total of 459,212 times. That is the number reported in the errors column for wsgi-nginx-uwsgi. This particular case had no socket connection, read, or write errors.

For Bottle, the peak performance was at 1,024 concurrency. We see Wrk reported 52 socket read errors and no other error types during that test.

Incidentally, in the results web site, the total requests completed is the requests count provided by Wrk less the errors. Looking at the wsgi-nginx-uwsgi results again, to determine RPS, we use code that works like this:

        totalErrors = httpErrors + socketErrors;
        rps = (totalRequestsReportedByWrk - totalErrors) / testDurationInSeconds;
        Hence for uwsgi-nginx-uswgi it is:
        (2117047 - 459212) / 15 = 110,522 successful RPS.  
