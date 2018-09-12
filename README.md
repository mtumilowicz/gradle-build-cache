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
Gradle logically has 3 layers of reuse that allow potentially 
expensive tasks from being executed:
1. Up-to-date checks for task outputs in the workspace.
1. A lookup for task outputs in the local cache.
1. A lookup for task outputs in a remote cache, such 
as the remote cache provided out of the box in Gradle Enterprise.

These 3 levels support making your builds faster in 3 different 
target scenarios:
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

