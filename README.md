# sbt-git #

[![Join the chat at https://gitter.im/sbt/sbt-git](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/sbt/sbt-git?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

The `sbt-git` plugin offers git command line features directly inside sbt as
well as allowing other plugins to make use of git.


## Installation ##
As of `0.10.0` (not released yet) this plugin requires at least **Java 8**.
The latest version supporting Java 7 was `0.9.3`, while the latest version supporting Java 6 was `0.8.5`.

Latest:

Add the following to your `project/plugins.sbt` or `~/.sbt/0.13/plugins/plugins.sbt` file:

    addSbtPlugin("com.typesafe.sbt" % "sbt-git" % "0.9.3")

Prior to sbt 0.13.5:

Add the following to your `project/plugins.sbt` or `~/.sbt/0.13/plugins/plugins.sbt` file:

    addSbtPlugin("com.typesafe.sbt" % "sbt-git" % "0.7.1")

additionally, use one of the older README.md files: (https://github.com/sbt/sbt-git/blob/v0.7.1/README.md)

### Using JGit ###

If you do not have git installed and available on your path (e.g. you use windows),
make sure your `git.sbt` or `~/.sbt/0.13/git.sbt` file looks like this:

    useJGit

Or you can type this into the prompt:

    > set useJGit
    [info] Reapplying settings...
    [info] Set current project to scala-arm (in build file:...)
    > session save
    [info] Reapplying settings...
    [info] Set current project to scala-arm (in build file:...)

This will enable a java-only GIT solution that, while not supporting all the same
commands that can be run in the standard git command line, is good enough for most
git activities.


## Versioning with Git ##

You can begin to use git to control your project versions.

    enablePlugins(GitVersioning)

The git plugin will now autogenerate your version using the following rules, in order:

1. Looks at version-property setting (default to `project.version`), and checks the `sys.props` to see if this has a value.  If so, use it.
2. Otherwise, looks at the project tags.  The first to match the `gitTagToVersionNumberSetting` is used to assign the version.  The default is to look for tags that begin with `v` and a number, and use the number as the version.
3. If no tags are found either, look at the head commit. We attach this to the `git.baseVersion` setting: "&lt;base-version&gt;.&lt;git commit sha&gt;"
4. If no head commit is present either (which means this is a brand-new repository with no commits yet), we append the current timestamp to the base version: "&lt;base-version&gt;.&lt;timestamp&gt;".

The `git.baseVersion` setting represents the in-development (upcoming) version you're working on.

You can alter the tag-detection algorithm using the `git.gitTagToVersionNumber` setting. For example, if we wanted to alter the default version tag detection so it does not require a "v" at the start of tags, we could add the following setting:

    git.gitTagToVersionNumber := { tag: String =>
      if(tag matches "[0-9]+\\..*") Some(tag)
      else None
    }

You can turn on version detection using `git describe` command by adding:

    git.useGitDescribe := true

This way the version is derived by passing the result of `git describe` to the `gitTagToVersionNumber` function. The `describe` version is built from the last tag + number of commits since tag + short hash.  We recommend adopting the git describe approach.

Additionally, you can also customize the version number generated by overriding one of the following keys:

* `git.formattedShaVersion` - Should look up `git.gitHeadCommit` key and generate a version based on it.
* `git.formattedDateVersion` - The version we'll use if git is unavailable on this repository, for some reason.

As an example, you can alter the default sha-based versions using the following code

    git.formattedShaVersion := git.gitHeadCommit.value map { sha => s"v$sha" }


## Prompts ##

You can use the git plugin to add the project name + the current branch to your prompt. Simply add this setting to every project:

    enablePlugins(GitBranchPrompt)

## Usage ##

In an sbt prompt, simply enter any git command.  e.g.

    > git status
    # On branch master
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #	modified:   build.sbt
    #	modified:   project/plugins/project/Build.scala
    #
    # Untracked files:
    #   (use "git add <file>..." to include in what will be committed)
    #
    #	src/site/
    no changes added to commit (use "git add" and/or "git commit -a")


## Known issues
When running sbt, you will see the following warnings in console:
```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

To get rid of them, you can force the SLF4J no-op binder by adding `libraryDependencies += "org.slf4j" % "slf4j-nop" % "1.7.21"` to `~/.sbt/0.13/plugins/plugins.sbt`

## Running the tests

Tests for this plugin are written using `sbt-scripted`. Test can be executed with 

```
sbt scripted
```

## Licensing ##

This software is licensed under the BSD license.
