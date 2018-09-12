# gradle-build-cache
Simple configuration of Gradle build cache.

_Reference_: https://docs.gradle.com/enterprise/tutorials/caching/  
_Reference_: https://docs.gradle.org/current/userguide/build_cache.html

# preface
The Gradle build cache is a cache mechanism that aims to save time 
by reusing outputs produced by other builds. The build cache works 
by storing (locally or remotely) build outputs and allowing builds 
to fetch these outputs from the cache when it is determined that 
inputs have not changed, avoiding the expensive work of regenerating 
them.

A first feature using the build cache is task output caching. 
Essentially, task output caching leverages the same intelligence 
as up-to-date checks that Gradle uses to avoid work when a previous 
local build has already produced a set of task outputs. But instead 
of being limited to the previous build in the same workspace, task 
output caching allows Gradle to reuse task outputs from any earlier 
build in any location on the local machine. When using a shared build 
cache for task output caching this even works across developer machines 
and build agents.

# overview
1. Between consecutive runs of a Gradle build by developers, 
usually not many things change. The Gradle incremental build 
feature will only execute the tasks that are not up-to-date 
anymore since they were last executed.
1. Developers typically maintain many workspaces on many branches 
to perform logically distinct tasks. The local cache allows outputs 
to be quickly reused across workspaces and branches without having 
to transit any networks.
1. Oftentimes, CI nodes and developers run the same tasks with the 
same set of changes. The remote cache allows outputs to be reused 
across users and build agents, ensuring your team never has to build 
the same thing twice.

# enabling build cache
By default, the build cache is not enabled. To enable it:
put `org.gradle.caching=true` in `gradle.properties`.

When the build cache is enabled, it will store build outputs in the 
Gradle user home.

# manual
1. enable build cache (`gradle.properties`)  
    `org.gradle.caching=true`
1. configure build cache (`settings.gradle`)
    ```
    buildCache {
        local(DirectoryBuildCache) {
            directory = new File(rootDir, 'build-cache') // custom project's directory
            removeUnusedEntriesAfterDays = 30
        }
    }
    ```
1. run `build` for the first time
    ```
    > Task :compileJava
    > Task :processResources NO-SOURCE
    > Task :classes
    > Task :jar
    > Task :assemble
    > Task :compileTestJava NO-SOURCE
    > Task :processTestResources NO-SOURCE
    > Task :testClasses UP-TO-DATE
    > Task :test NO-SOURCE
    > Task :check UP-TO-DATE
    > Task :build
    ```
1. delete `build` directory
1. run `build` again
    ```
    > Task :compileJava FROM-CACHE
    > Task :processResources NO-SOURCE
    > Task :classes UP-TO-DATE
    > Task :jar
    > Task :assemble
    > Task :compileTestJava NO-SOURCE
    > Task :processTestResources NO-SOURCE
    > Task :testClasses UP-TO-DATE
    > Task :test NO-SOURCE
    > Task :check UP-TO-DATE
    > Task :build
    ```
    _Remark_: `compileJava` is taken from cache

* For educational purposes we've committed exemplary `build-cache`
directory.