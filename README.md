ch.vorburger.exec
=================

_If you like/use this project, a Star / Watch / Follow me on GitHub is appreciated!_

[![Maven Central](https://maven-badges.herokuapp.com/maven-central/ch.vorburger.exec/exec/badge.svg)](https://maven-badges.herokuapp.com/maven-central/ch.vorburger.exec/exec)
[![Javadocs](http://www.javadoc.io/badge/ch.vorburger.exec/exec.svg)](http://www.javadoc.io/doc/ch.vorburger.exec/exec)
[![Build Status](https://travis-ci.org/vorburger/ch.vorburger.exec.svg?branch=master)](https://travis-ci.org/vorburger/ch.vorburger.exec)

This project is a small library allowing to launch external processes from Java code in the background,
and conveniently correctly pipe their output e.g. into slf4j, await either their termination or specific output, etc.

[Release Notes are in CHANGES.md](CHANGES.md).

Usage
---

Launching external processes from Java using the raw java.lang.ProcessBuilder API directly can be a little cumbersome.
[Apache Commons Exec](https://commons.apache.org/proper/commons-exec/) makes it a bit easier, but lacks some convenience.
This library makes it truly convenient:

```java
ManagedProcessBuilder pb = new ManagedProcessBuilder("someExec")
    .addArgument("arg1")
    .setWorkingDirectory(new File("/tmp"))
    .getEnvironment().put("ENV_VAR", "...")
    .setDestroyOnShutdown(true)
    .addStdOut(new BufferedOutputStream(new FileOutputStream(outputFile)))
    .setConsoleBufferMaxLines(7000);  // used by startAndWaitForConsoleMessageMaxMs

ManagedProcess p = pb.build();
p.start();
p.isAlive();
p.waitForExit();
// OR: p.waitForExitMaxMsOrDestroy(5000);
// OR: p.startAndWaitForConsoleMessageMaxMs("Successfully started", 3000);
p.exitValue();
// OR: p.destroy();

// This works even while it's running, not just when it exited
String output = p.getConsole();
```

It currently internally uses Apache Commons Exec by building on top, extending and wrapping it,
but without exposing this in its API, so that theoretically in the future this implementation detail could be changed.

History
---

Historically, this code was part of [MariaDB4j](https://github.com/vorburger/MariaDB4j/) (and this is why it's initial version was 3.0.0),
but was it later split into a separate project. This was done to make it usable in separate projects
(originally for a possible [POC to launch Ansible Networking CLI commands](https://github.com/vorburger/opendaylight-ansible/)
from [OpenDaylight](http://www.opendaylight.org)).

ToDo
---

This library is currently used to control daemon style external executables.
To launch a process which returns binary (or massive textual) output to its STDOUT
(and, presumably, have that piped into a java.io.OutputStream), it would need some tweaks.
This would include making the enabled-by-default logging into slf4j, and the built-in
startAndWaitForConsoleMessageMaxMs which collects output, a more configurable option.

Contributions & patches more than welcome!
