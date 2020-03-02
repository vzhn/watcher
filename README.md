## Watcher (fizzed-watcher-maven-plugin)

Ever wish Maven could run a specific command if any files in your project change? Some IDEs
have their own features to do X if a file changes, but they usually don't take your full Maven
project file into account. Problem finally solved with the Watcher Plugin for Maven. Add to your
Maven project file and fire it up in a new shell. Maven will continuously and recursively
watch any number of configured directories and then execute a series of goals if a file change
is detected. Just like if you typed it on the command-line! We use it at Fizzed across a bunch
of projects, but mainly our web projects -- where we want a full compile run any time our Java
code, resources, or templates change. Works especially well from a project parent to watch any
of your sub modules too.

To assist in using this plugin to trigger other workflow/plugins, v1.0.6 added
a feature to "touch" a file upon successful execution of the maven goal(s).

To use type that command:
```$bash
mvn -Dwatches=./first-project/src/main,./second-project/src/main -Dprojects=first-project,second-project -Dgoals=install -Dprofiles=optional
-profile io.github.vzhn:watcher-maven-plugin:1.0.7-SNAPSHOT:run
```

Or add the following to your POM:

```xml
<build>
    <plugins>
        ...
        <plugin>
            <groupId>io.vzhn</groupId>
            <artifactId>watcher-maven-plugin</artifactId>
            <version>1.0.7-SNAPSHOT</version>
            <configuration>
                <touchFile>target/classes/watcher.txt</touchFile>
                <watches>
                    <watch>
                        <directory>core/src/main/java</directory>
                    </watch>
                    <watch>
                        <directory>ninja/src/main/java</directory>
                    </watch>
                </watches>
                <goals>
                    <goal>compile</goal>
                    <goal>process-classes</goal>
                </goals>
                <profiles>
                    <profile>optional-profile-to-activate</profile>
                </profiles>
            </configuration>
        </plugin>
        ...
    </plugins>
</build>
```

Each watch entry may also contain include and exclude properties as well as
enabling/disabling of recursively watching a directory.  Here is an example of
watching a directory, but excluding files with a suffix of *.html.

```xml
<watch>
    <directory>src/main/java</directory>
    <exclude>*.html</exclude>
</watch>
```

You may add any number of exclude and include entries.  The recursive property
can be set to true/false to disable/enable recursively watching a directory.

By default this maven plugin does NOT attach to a lifecycle -- since it is 
essentially a daemon that runs forever.  Usually, you'll run this in a separate
shell and run via:

```bash
mvn fizzed-watcher:run
```

## License

Copyright (C) 2015 Fizzed, Inc.

This work is licensed under the Apache License, Version 2.0. See LICENSE for details.