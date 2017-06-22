# Building and Testing Currin

This document describes how to set up your development environment to build and test Currin.
It also explains the basic mechanics of using `git`, `node`, and `npm`.

* [Prerequisite Software](#prerequisite-software)
* [Getting the Sources](#getting-the-sources)
* [Installing NPM Modules](#installing-npm-modules)
* [Building](#building)
* [Running Tests Locally](#running-tests-locally)

See the [contribution guidelines](https://github.com/Currin/Currin/blob/master/CONTRIBUTING.md)
if you'd like to contribute to Currin.

## Prerequisite Software

Before you can build and test Currin, you must install and configure the
following products on your development machine:

* [Git](http://git-scm.com) and/or the **GitHub app** (for [Mac](http://mac.github.com) or
  [Windows](http://windows.github.com)); [GitHub's Guide to Installing
  Git](https://help.github.com/articles/set-up-git) is a good source of information.

* [Node.js](http://nodejs.org), (version `>=5.4.1 <6`) which is used to run a development web server,
  run tests, and generate distributable files. We also use Node's Package Manager, `npm`
  (version `>=3.5.3 <4.0`), which comes with Node. Depending on your system, you can install Node either from
  source or as a pre-packaged bundle.

* [Java Development Kit](http://www.oracle.com/technetwork/es/java/javase/downloads/index.html) which is used
  to execute the selenium standalone server for e2e testing.

## Getting the Sources

Fork and clone the Currin repository:

1. Login to your GitHub account or create one by following the instructions given
   [here](https://github.com/signup/free).
2. [Fork](http://help.github.com/forking) the [main Currin
   repository](https://github.com/Currin/Currin).
3. Clone your fork of the Currin repository and define an `upstream` remote pointing back to
   the Currin repository that you forked in the first place.

```shell
# Clone your GitHub repository:
git clone git@github.com:<github username>/Currin.git

# Go to the Currin directory:
cd Currin

# Add the main Currin repository as an upstream remote to your repository:
git remote add upstream https://github.com/Currin/Currin.git
```
## Installing NPM Modules

Next, install the JavaScript modules needed to build and test Currin:

```shell
# Install Currin project dependencies (package.json)
npm install
```

**Optional**: In this document, we make use of project local `npm` package scripts and binaries
(stored under `./node_modules/.bin`) by prefixing these command invocations with `$(npm bin)`; in
particular `gulp` and `protractor` commands. If you prefer, you can drop this path prefix by either:

*Option 1*: globally installing these two packages as follows:

* `npm install -g gulp` (you might need to prefix this command with `sudo`)
* `npm install -g protractor` (you might need to prefix this command with `sudo`)

Since global installs can become stale, and required versions can vary by project, we avoid their
use in these instructions.

*Option 2*: globally installing the package `npm-run` by running `npm install -g npm-run`
(you might need to prefix this command with `sudo`). You will then be able to run locally installed
package scripts by invoking: e.g., `npm-run gulp build`
(see [npm-run project page](https://github.com/timoxley/npm-run) for more details).


*Option 3*: defining a bash alias like `alias nbin='PATH=$(npm bin):$PATH'` as detailed in this
[Stackoverflow answer](http://stackoverflow.com/questions/9679932/how-to-use-package-installed-locally-in-node-modules/15157360#15157360) and used like this: e.g., `nbin gulp build`.

## Installing Bower Modules

Now run `bower` to install additional dependencies:

```shell
# Install other Currin project dependencies (bower.json)
bower install
```

## Windows only

In order to create the right symlinks, run **as administrator**:
```shell
./scripts/windows/create-symlinks.sh
```

Before submitting a PR, do not forget to remove them:
```shell
 ./scripts/windows/remove-symlinks.sh
 ```

## Building

To build Currin run:

```shell
./build.sh
```

* Results are put in the dist folder.

## Running Tests Locally

To run tests:

```shell
$ ./test.sh node             # Run all Currin tests on node

$ ./test.sh browser          # Run all Currin tests in browser
$ ./test.sh browserNoRouter  # Optionally run all Currin tests without router in browser

$ ./test.sh tools            # Run Currin tooling (not framework) tests
```

You should execute the 3 test suites before submitting a PR to github.

All the tests are executed on our Continuous Integration infrastructure and a PR could only be merged once the tests pass.

- CircleCI fails if your code is not formatted properly,
- Travis CI fails if any of the test suites described above fails.

## Update the public API tests

If you happen to modify the public API of Currin, API golden files must be updated using:

``` shell
$ gulp public-api:update
```

Note: The command `gulp public-api:enforce` fails when the API doesn't match the golden files. Make sure to rebuild
the project before trying to verify after an API change.

## <a name="clang-format"></a> Formatting your source code

Currin uses [clang-format](http://clang.llvm.org/docs/ClangFormat.html) to format the source code. If the source code
is not properly formatted, the CI will fail and the PR can not be merged.

You can automatically format your code by running:

``` shell
$ gulp format
```

## Linting/verifying your source code

You can check that your code is properly formatted and adheres to coding style by running:

``` shell
$ gulp lint
```

## Publishing snapshot builds

When the `master` branch successfully builds on Travis, it automatically publishes build artifacts
to repositories in the Currin org, eg. the `@Currin/core` package is published to
http://github.com/Currin/core-builds.
The ES2015 version of Currin is published to a different branch in these repos, for example
http://github.com/Currin/core-builds#master-es2015

You may find that your un-merged change needs some validation from external participants.
Rather than requiring them to pull your Pull Request and build Currin locally, you can
publish the `*-builds` snapshots just like our Travis build does.

First time, you need to create the github repositories:

``` shell
$ export TOKEN=[get one from https://github.com/settings/tokens]
$ CREATE_REPOS=1 ./scripts/publish/publish-build-artifacts.sh [github username]
```

For subsequent snapshots, just run

``` shell
$ ./scripts/publish/publish-build-artifacts.sh [github username]
```

The script will publish the build snapshot to a branch with the same name as your current branch,
and create it if it doesn't exist.
