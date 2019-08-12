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

Remove `artifact-to-be-removed:1.0` from local repository.

```
$ rm -rf ~/.m2/repository/suztomo/artifact-to-be-removed/1.0
```

Now module-a does not have its dependency `artifact-to-be-removed:1.0`.

Keep `artifact-to-be-removed:2.0` there.

# module-b succeeds to run Maven

Module-b depends on module-a and `artifact-to-be-removed:2.0`.

Even without `artifact-to-be-removed:1.0` used by module-a, module-b succeeds to run Maven,
because Maven ignores the missing dependency. It just outputs warning message.

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

# module-c fails to run Maven

Module-c fails to run Maven.

The only difference between module-b and module-c is repository section in the pom.

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