The relevant code for Travis-CI within TFB is within the [toolsets directory](https://github.com/TechEmpower/FrameworkBenchmarks/tree/master/toolset), which contains the code that TFB uses to automate installing, launching, load testing, and terminating each framework.

## Travis Integration

This section details how [TFB](https://github.com/TechEmpower/FrameworkBenchmarks) integrates with [Travis Continuous Integration](https://travis-ci.org/TechEmpower/FrameworkBenchmarks). At a high level, there is a github hook that notifies travis-ci every time new commits are pushed to master, or every time new commits are pushed to a pull request. Each push causes travis to launch a virtual machine, checkout the code, run an installation, and run a verification.

### Travis Terminology

Each push to github triggers a new travis *build*. Each *build* contains a number of independent *jobs*. Each *job* is run on an isolated virtual machine called a *worker*. 

Our project has one *job* for each framework directory, e.g. one *job* for `go`, one *job* for `activeweb`, etc. Each *job* gets it's own *worker* virtual machine that runs the installation for that framework and verifies the framework's output using `--mode verify`. 

The *.travis.yml* file specifies the *build matrix*, which is the set of *jobs* that should be run for each *build*. Our *build matrix* lists each framework directory, which causes each *build* to have one *job* for each listed directory. 

If any *job* is *canceled*, then the *build* is marked *canceled*.
If any *job* returns *error*, then the *build* is marked *errored*.
If any *job* returns *fail*, then the *build* is marked *failed*.
The *build* is marked *pass* only if every *job* returns *pass*. 

### Travis Limits

[Travis-ci.org](https://travis-ci.org/) is a free ([pro available](https://travis-ci.com/)) service, and therefore imposes multiple limits. 

Each time someone pushes new commits to master (or to a pull request), a new *build* is triggered that contains ~180 *jobs*, one for each framework directory. This obviously is resource intensive, so it is critical to understand travis limits. 

**Minutes Per Job**: `50 minutes` maxiumum. None of the *job*s we run hit this limit (most take 10-15 minutes total)

**Max Concurrent Jobs**: `4 jobs`, but can increase to 10 if Travis has low usage. This is our main limiting factor, as each *build* causes ~180 *jobs*. Discussed below

**Min Console Output**: If `10 minutes` pass with no output to stdout or stderr, Travis considers the *job* as errored and halts it. This affects some of our larger tests that perform part of their installation inside of their `setup.py`. Discussed below

**Max Console Output**: A *job* can only ouput `4MB` of log data before it is terminated by Travis. Some of our larger builds (e.g. `aspnet`) run into this limit, but most do not

### Dealing with Travis' Limits

**Max Concurrent Jobs**: Basically, we rapidly return `pass` for any unneeded jobs. Practically this is entirely handled by `run-ci.py`. If needed, the TechEmpower team can manually `cancel` *jobs* (or *builds*) directly from the Travis website. Every *build* queues every *job*, there is no way to not queue *jobs* we don't need, so the only solution is to rapidly quit unneeded jobs. We previously used a solution that would automatically cancel *jobs*, but if any job is canceled the entire *build* is canceled. 

**Max Console Output**: Some jobs do lots of compiling, and Travis-CI will abort the job if there is too much output. At the moment this is very rare (e.g. only wt, which downloads+compiles both mono and wt, has experienced this issue). The only solution is to reduce the amount of output you generate. 

### Tricks and Tips for Travis-CI

**Use your own Travis Queue**: We cannot stress this enough. Your own queue will help you work 10x faster, and it's easy to setup. Just go to travis-ci.org, click log in with Github, and enable Travis-CI on your fork of TFB. 

**Use the Travis-CI Command Line to Quickly Cancel Jobs**: Travis has a ruby command line. After you install it and log into your Github account, you can do something like this: 

    $ for i in {21..124}
    do
      travis cancel -r hamiltont/FrameworkBenchmarks 322.$i &
    done

Note the fork `&` at the end - the Travis command line client seems to have very high latency, so doing this loop can take 30 minutes to complete. If you fork each job then the loop will complete in seconds and the jobs will still be canceled properly. 

**How to see log files on Travis-CI**: You may occasionally need to see a log file. You can use your setup.py's stop function to cat the file to stdout. 
    
    subprocess.call('cat <my-log-file>', shell=True, stdout=logfile)

This may cause issues with the Travis-CI **Max Console Output** limitation. An alternative is to upload the file to an online service like sprunge: 

    subprocess.call("cat <my-log-file> | curl -F 'sprunge=<-' http://sprunge.us", shell=True, stdout=logfile)

If you need to use these solutions, please be sure to remove them (or comment them out) before you send in a pull request.

### Advanced Travis Details

#### Partial vs Full Verification

Travis first checks if there is any reason to run a verification for this framework. This check uses `git diff` to list the files that have been modified. If files relevant to this framework have not been modified, then Travis doesn't bother running the installation (or the verification) as nothing will have changed from the last build. We call this a **partial verification**, and if only one framework has been modified then the entire build will complete within 10-15 minutes. 

If any of the setup scripts within the `toolset/` has changed, `travis_diff` attempts to find only the frameworks that would be affected by this and run only those jobs. Other files within the `toolset/` that are not setup scripts will run all jobs. We call this a **full verification**, and the entire build will complete within 4-5 hours. Once the build completes, Travis stops listing the wall time of 5 hours and instead uses the CPU time of ~20 hours total. 

Travis returns `pass` for any *job* that we do not need to test. If any job is passed for this reason, that is a partial verification. Each job is examined separately, and *jobs* that should be tested are run as normal. The final status for the verification of the pull request will therefore depend on the exit status of the *jobs* that are run as normal - if they return `pass` then the entire build will `pass`, and similarly for fail. 

For example, if files inside `aspnet/` are modified as part of a pull request, then every *job* but `aspnet` is guaranteed to return `pass`. The return code of the `aspnet` job will then determine the final exit status of the build. 

#### Travis Commit Message Hooks

Travis provides a commit message hook and we've created a few more to help make dealing with Travis and the Partial vs Full Verification a little easier. You can add any of the following to your commit message when pushing to a Travis enabled repo. Each Travis build evaluates the most recent commit message.

* `[ci skip]` - This is provided by Travis. If this is found in your commit message, Travis will not trigger at all.
* `[ci run-all]` - This will run all the jobs despite what files have been changed.
* `[ci fw <testdirs>]` - This will run the tests for the selected framework directories *in addition to* the jobs that would normally trigger from the files that have been changed. Example: `[ci fw JavaScript/express Java/gemini]`
* `[ci fw-only <testdirs>]` - The same as above except it will *only* run these tests despite the files that have been modified. Example: `[ci fw-only JavaScript/express Java/gemini]`
* `[ci lang <testlang>]` - This will run the tests for the selected language *in addition to* the jobs that would normally trigger from the files that have been changed. Example: `[ci lang JavaScript Java]`
* `[ci lang-only <testlang>]` - The same as above except it will *only* run these language tests despite the files that have been modified. Example: `[ci fw-only JavaScript Java]`


#### Running Travis in a Fork

A Travis account specific to your fork of TFB is highly valuable, as you have personal limits on *workers* and can therefore see results from Travis much more quickly than you could when the Travis account for TechEmpower has a full queue awaiting verification. 

#### Important Notes About Travis-CI

If you make changes to configuration files, or files outside your framework directory, Travis will run tests on all existing frameworks. For this reason, it may appear that your tests have failed. Be sure to check your Travis build by clicking on the checkmark or red 'X' to dig into your specific test.

Be sure to keep your Pull Request branch up-to-date with the target branch, otherwise our diffing tool may detect additional changes causing unwanted tests to run.
