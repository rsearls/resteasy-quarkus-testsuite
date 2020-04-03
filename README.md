# resteasy-quarkus-testsuite

This project runs RESTEasy's _integration-tests_ module using quarkus.
The objective is to help the quarkus team identify problems in the 
handling of jaxrs code, identify code regressions, and provide
a project for CI.
  
There are four reasons quarkus testing of module _integration-tests_ has 
been moved out of RESTEasy's project workspace.
* The standard build configurations for RESTEasy and quarkus
  are not compatible in RESTEasy's maven build framework.
* The objective is to run quarkus on the tests from RESTEasy's
  _integration-tests_ and not retrain duplicates of these test files.
* Currently a large percentage of these tests fail; quarkus is still 
  an evolving product.  We do not want this to impact the every day 
  development cycle of RESTEasy.
* The testsuite can take quite a while to run.  Again we do want this
  to impact the RESTEasy development cycle.      
    
## Project Structure
This is not a traditional maven project.  There are local POM files
and a local module directory, _integration-tests_, but the source code 
for RESTEasy's _integration-tests_ module is pulled in to the project 
via archive, _resteasy-integration-tests-VERSION-sources.zip_
and installed in directory _integration-tests/src_.

Directory _integration-tests/src/main_ is provided as a part of this project.
The files in this directory are provided so _quarkus-maven-plugin_
can successfully complete its processing.

The _maven-dependency-plugin_ has been configured so that it will 
not unpack and overwrite the test files from a previous run. This 
is to ensure changes you make locally will not be overwritten 
in successive runs. 

Command **'mvn clean'** does not remove the files from _integration-tests/src/test_.
It only removes the _target_ directories.


## Project Profiles
Two maven profiles have been defined in this project.
* **testing-default**: is the default profile.  It runs if no profile is 
    specified on the command-line.  This profile runs all the tests.
    Currently the quarkus execution environment interfers with the printing 
    of each surefire test statistic.  There is no way to know which test passed
    and which failed.
    https://github.com/quarkusio/quarkus/issues/7122
* **test-exclusion**: is provided to allow running of individual tests.
    It uses the _maven-surefire-plugin_ excludes configuration feature.
    The user will need to edit the _integration-tests/pom.xml_ file
    to enable/disable the desired test.

## Project Prerequisites
This project must use quarkus's execution environment prerequisites.
Currently (Apr 3, 2020) they as follows.
* maven version 3.6.3 or higher
* JDK 11 or higher


## Building the Project

* The initial installation of the test files can be accomplished several ways.
    * **'mvn initialize'**        unpacks the zip achive only.
    * **'mvn clean install -DskipTests'**     upacks the zip achive, 
        compiles the classes, does not run the tests.

## Running the Tests
Tests can be run from the project root directory or from within directory
_integration-tests_.
* **'mvn test'** uses the _testing-default_ profile to run all the tests.
* **'mvn test -P test-exclusion'** uses profile _test-exclusion_ to run
        individual tests.
        
## Test debugging
To debug individual tests use the _test-exclusion_ profile in conjunction with
surefire flag _-Dmaven.surefire.debug_.  In your IDE set breakpoints
and configure a remote debugger for port 5005.  Edit the exclude list in
profile _test-exclusion_ and run the test as follows.
* **mvn test -P test-exclusion -Dmaven.surefire.debug'**  

Occasionally there is a failure during the compiling process.  Currently
little helpful information is provided.  To further debug the issue it is
desirable to debug the maven plugin.  To do this define the _MAVEN_OPTS_
environment variable as below.  Set the jdwp address to the desired remote 
debugger port number.  Run the execution command above and attach the remote 
debugger to the running process.  
* **export MAVEN_OPTS="-agentlib:jdwp=transport=dt_socket,address=8787,server=y,suspend=y"**      



## Test Code Changes
If a RESTEasy test file requires a change.  The change will need to be implemented 
in RESTEasy's _integration-tests_ module.  Please file a RESTEasy jira 
for the change.  
https://issues.redhat.com/projects/RESTEASY/issues

Test failures identified to be a quarkus issue should be filed with quarkus.
https://github.com/quarkusio/quarkus/issues/

Changes needed to this project's local files should be registered as an
issue on this project's github site.


