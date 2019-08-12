

```
$ pwd
/usr/local/google/home/suztomo/maven-missing-artifact
$ mvn install
$ rm -rf ~/.m2/repository/suztomo/artifact-to-be-removed/1.0
```



```
$ cd module-b
$ mvn install
...
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  1.327 s
[INFO] Finished at: 2019-08-12T14:46:02-04:00
[INFO] ------------------------------------------------------------------------
```

