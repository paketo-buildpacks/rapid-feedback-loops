# Watchexec Investigation

## Findings

### Configuration Options
`watchexec` has a variety of options that make it flexible, beyond its default behaviour. We outline a few of these here, focusing
on those that may be relevant to buildpack developers working with `watchexec` in start commands.

#### Process Management

`watchexec` creates a process group by default when it is invoked. The
watchexec process becomes the parent of the process group and the command
passed as an argument becomes a member of that group.

`watchexec`'s default behaviour of _not_ interrupting its child process if it's
in progress poses an issue for many buildpacks applications. Most buildpack
builds result in containers that run long-running servers. `watchexec
--restart` tells `watchexec` to interrupt its currently-running child process
and start a new one. To stop the current child, it sends `SIGTERM` (by
default). It creates a new child process once it receives `SIGCHLD` from the
previous one.

Users can configure the signal that `watchexec` sends to terminate its child on restart, with
`--signal`. This may be useful if the process `watchexec` is managing has certain signal
handlers that a user does/doesn't want to invoke.

Watchexec executes the command passed to it by invoking it through a child
shell process ("sh -c <cmd>"). Using --shell=none will force watchexec to forego the shell and
invoke the command directly.

The --on-busy-update flag can be used to specify what action watchexec will
take when it observes a file modification event. --restart mentioned above is
shorthand for "--on-busy-update=restart". Other options include: do-nothing,
queue, signal.


#### Filtering File Change Events
`watchexec` triggers on modifications to any files in its current working directory (and subdirectories) by default. Several flags
enable users to fine-tune which files should be watched.

The flags `--filter` and `--exts` tell `watchexec` to pay attention to files
that match the user's parameters. `--exts` takes a comma-separated list of file
extensions (e.g. `--exts js,txt`). `watchexec` will only trigger on changes to
files with those extensions. `--filter` accepts file glob patterns (which can
match parent directories of the current working directory). In the context of
the Node.js buildpack, for example, it may be useful watch only the `*.js`
files within the app directory. 

The `--watch` flag `watchexec` overrides the default behaviour of watching the
current directory. Instead, `watchexec` will watch only the files in the dirs
passed into the flag. This flag can be included multiple times, as in watchexec
-w /app -w /node-modules node server.js. 

The `ignore` flag tells watchexec which files to ignore modifications for. This
flag accepts glob patterns. By default, watchexec excludes a set of
commonly-ignored glob patterns (what are these patterns?). The
--no-default-ignore flag disables this behaviour. Watchexec will also exclude
patterns which appear inside a .gitignore or .ignore file in the project
directory. This can be disabled using the --no-vcs-ignore flag.



## Unresolved Questions
* shell interpolation within the watchexec proc, to shell or no-shell?
* proc group good or bad?

