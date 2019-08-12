This repository demonstrates how having repository section affects Maven's dependency resolution
even when a missing dependency is unnecessary in final dependency graph.

# Setup

Install the modules into local repository.

```
$ pwd
/usr/local/google/home/suztomo/maven-missing-artifact
$ mvn install
...
[INFO] artifact-to-be-removed v1 is to be removed 1.0 ..... SUCCESS [  0.735 s]
[INFO] artifact-to-be-removed v2 is not to be removed 2.0 . SUCCESS [  0.054 s]
[INFO] maven-missing-artifact 1.0 ......................... SUCCESS [  0.006 s]
[INFO] module-a which has missing dependency 1.0 .......... SUCCESS [  0.051 s]
[INFO] module-b 1.0-SNAPSHOT .............................. SUCCESS [  0.056 s]
[INFO] module-c 1.0-SNAPSHOT .............................. SUCCESS [  0.048 s]
```

Remove `artifact-to-be-removed:1.0` from local repository:

```
$ rm -rf ~/.m2/repository/suztomo/artifact-to-be-removed/1.0
```

Now module-a's dependency `artifact-to-be-removed:1.0` is missing.

(Keep `artifact-to-be-removed:2.0` in the local repository.)

# Comparison between module-b and module-c

Now let's run comparison between module-b and module-c. Both have the same dependency section.

## module-b succeeds to run Maven

Module-b depends on module-a and `artifact-to-be-removed:2.0`. Module-b succeeds to run Maven.

The absence of `artifact-to-be-removed:1.0` is not significant, because the final dependency graph
only needs `module-a` and `artifact-to-be-removed:2.0`, both of which exist in local Maven repository.

It just outputs warning message for missing `artifact-to-be-removed:1.0`.

```
$ cd module-b
$ mvn install
...
[WARNING] The POM for suztomo:artifact-to-be-removed:jar:1.0 is missing, no dependency information available
...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.327 s
[INFO] Finished at: 2019-08-12T14:46:02-04:00
[INFO] ------------------------------------------------------------------------
```

## module-c fails to run Maven

Module-c also depends on module-a and `artifact-to-be-removed:2.0`. Module-b fails to run Maven.

Module-c gets the error `Unknown host snapshots.maven.codehaus.org`, even though the final dependency
graph would only need `module-a` and `artifact-to-be-removed:2.0`, both of which are available in local
Maven repository.

```
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.466 s
[INFO] Finished at: 2019-08-12T14:58:42-04:00
[INFO] ------------------------------------------------------------------------
[ERROR] Failed to execute goal on project module-c: Could not resolve dependencies for project suztomo:module-c:jar:1.0-SNAPSHOT: Failed to collect dependencies at suztomo:module-a:jar:1.0 -> suztomo:artifact-to-be-removed:jar:1.0: Failed to read artifact descriptor for suztomo:artifact-to-be-removed:jar:1.0: Could not transfer artifact suztomo:artifact-to-be-removed:pom:1.0 from/to codehausSnapshots (http://snapshots.maven.codehaus.org/maven2): snapshots.maven.codehaus.org: Name or service not known: Unknown host snapshots.maven.codehaus.org: Name or service not known -> [Help 1]
[ERROR] 
```

The only difference between module-b and module-c is repository section in the pom.

```
$ diff module-b/pom.xml module-c/pom.xml
13c13
<   <artifactId>module-b</artifactId>
---
>   <artifactId>module-c</artifactId>
15c15
<   <name>module-b that succeeds Maven</name>
---
>   <name>module-c that fails to run Maven</name>
32a33,43
> 
>   <!-- The only difference from module-b -->
>   <repositories>
>     <repository>
>       <id>codehaus</id>
>       <name>Retired Repository</name>
>       <!-- This website has been retired -->
>       <url>http://repository.codehaus.org/</url>
>       <layout>default</layout>
>     </repository>
>   </repositories>
```
