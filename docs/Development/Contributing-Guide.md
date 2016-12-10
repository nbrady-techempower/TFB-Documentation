# General Guidelines

These guidelines prevent us from having to give repeated feedback on 
the same topics: 

* __Use specific versions__: If you're updating any software or dependency, please be 
specific with the version number. Also, update the appropriate `README` to reflect 
that change. Don't rely on the package manager to deliver a specific version, apt 
consistently returns different versions on Ubuntu 12.04 vs 14.04.
* __Rope in experts__: If you're making a performance tweak, our team may not be 
able to verify your code--we are not experts in every language. It's always helpful 
to ping expert users to verify that the change is appropriate (a basic introduction
to someone with the appropriate credentials is also a plus). If you're an expert 
that is willing to be pinged occasionally, please add yourself to the appropriate test or
language README file. 
* __Use a personal Travis-CI account__: This one is mainly for your own sanity. Our 
[main Travis-CI](https://travis-ci.org/TechEmpower/FrameworkBenchmarks) can occasionally
become clogged with so many pull requests that it takes a day to finish all the 
builds. If you create a fork and enable Travis-CI.org, you will get your own 
build queue. This means 1) only your commits/branches are being verified, so there is 
no delay waiting for an unrelated pull request, and 2) you can cancel unneeded items. 
This does not affect our own Travis-CI setup at all--any commits added to a pull
request will be verifed as normal. 
* __Read the README__: We know that's cliche. However, our toolset drags in a lot of 
different concepts and frameworks, and it can really help to read the documentation and 
READMEs, such as this one, the ones inside specific language directories, and the
ones inside specific framework directories.
* __Use the Development Virtual Machine__: Our Vagrant scripts can set up a VM for you
that looks nearly identical to our test environment. This is even better than relying
on the Travis-CI verification, and you are strongly encouraged to use this. See 
the [installation guide](Installation-Guide#vagrant-development-environment) 
for specifics.

# Github Pull Request Procedure

If you'd like to submit a pull request for this documentation, see the
[about documentation page](../About/Documentation). Otherwise, follow the steps
below.

1. The branch that your branch will need to be based off of depends on the
purpose of the pull request.
    * For a bug fix based on preview results, base your branch off of the 
    next TFB round branch (`round-#`).
    * For anything else, base your branch off of `master`. These changes will 
    show up in the following round, so if we are currently in preview for 
    Round 14, any changes merged into `master` will be reflected in the Round
     15 results.
    * __Note:__ if we are not currently in a preview for an official round
    release, there will be no `round-#` branch and all fixes can be branched
     off of `master`. 
2. Submit your pull request from a branch in your
[forked repository](https://help.github.com/articles/fork-a-repo/) to make
changes and submit a pull request.
3. (optional) Clean up your commit(s) to provide future contributors with more
helpful information. Some sugguestions are:
    * Squash your commits into a clean, single commit (remove in-progress
    commits).
    * Rewrite your commit messages to be as detailed as possible.
4. Fetch and [rebase](https://help.github.com/articles/about-git-rebase/) off
of the appropriate TFB branch (`round-#` for a fix, `master` otherwise) prior
to opening the pull request to ensure that your branch has the latest updates
and a clean history.
5. If your PR is fixing an issue,
[tag the issue in the PR body](https://github.com/blog/1506-closing-issues-via-pull-requests)
if you haven't done so in a commit. Just include the
[special keyword syntax](https://help.github.com/articles/closing-issues-via-commit-messages/),
like "Resolves #1" and the issue will be automatically closed when your merge
request is merged in.
6. Open a PR from the branch on your repo with the target set to the
appropriate TFB branch (`round-#` for a bugfix, `master` otherwise). The PR
body will be prepopulated with a Pull Request Template explaining the
appropriate branch to use, if you are unsure.

# GitHub Issue Procedure
* You can submit an issue for almost anything, but also consider that some assistance can 
be provided by [contacting the community](../Support/Converse). Some valid (and encouraged!) 
example reasons to open an issue:
    * Found a bug
    * Framework/language README information out of date
* Add any appropriate labels/milestone tags for easier identification.
* If the issue is regarding this documentation, see the
[about documentation page](../About/Documentation).

# TFB Wiki
* The preferred method for suggesting the implementation of a new framework or language is adding it to the [TFB Wiki - Suggested Frameworks, Languages and Features](https://github.com/TechEmpower/FrameworkBenchmarks/wiki/Suggested-Frameworks,-Languages-and-Features) Page. 
