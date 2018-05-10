 This is the getting started guide for __development__. If you're interested in
__benchmarking__ please visit the [getting started guide for
benchmarking](../Benchmarking/Getting-Started-Benchmarking).
 
 1. __Get TFB__
     * [Fork](https://help.github.com/articles/fork-a-repo/) the [TFB
project](https://github.com/TechEmpower/FrameworkBenchmarks/) to your own
repository.
     * Clone your TFB repository to your local environment to get started.
 2. __Set up a development environment__  
     * We strongly recommended that you set up your TFB environment in a virtual
machine or on a remote server dedicated to this project, as the suite adds
users with escalated privileges, requires most ports to be open, installs
system software, and may uninstall or reinstall software that you already have.
Our production environments use 3 separate servers to run the database, the
load-generation system, and the web framework itself, but since you are getting
set up for development, you can run all 3 on a single machine. 
     * Get started quickly and [set up your development environment with
vagrant](Installation-Guide#vagrant-development-environment), or
     * [Click here for the guide to get your development environment set
up.](Installation-Guide) *Note: We provide
[scripts](../Codebase/Summary-of-Script-Directories) for configuring a Linux
development environment using either Virtualbox or Amazon EC2, and are actively
searching for help adding Windows.*
 3. __Enable Travis-CI__
     * [Enable Travis-CI](Testing-and-Debugging#travis-ci) on your project fork,
so that any commits you send to Github are automatically verified for
correctness (e.g. meeting all [benchmark
requirements](../Project-Information/Framework-Tests#requirements)).
 4. __Run a Test__
     * `tfb --mode verify --test ${your-test-here}` - to verify that your test
works.
     * `tfb --mode debug --test ${your-test-here}` - will just spin up your
server, allowing you time to debug the endpoints.
 5. __Clean Up__
     * Remove the __contents of /FrameworkBenchmarks/installs/__ if you'd like
to re-install the framework dependencies. 
       - You can remove the __results/__ and __installs__ directories by adding
the `--clean-all` option to the `run-tests.py` command
 
