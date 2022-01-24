# S1-02: Version Control Management (VCM)

Version control management is an important part of the workflow previously described. It ensures that as the developers, testers, and users use the sites in their various environments they can do so without issues by allowing them to track, commit and rollback changes in any of the files in their projects.

Imagine working on a project where there are several developers contributing to the same project. Each may be responsible for a different feature, but some of their work may overlap because they both may need to modify the same file in the project. Version control allows for this eventuality to happen safely by tracking all the changes made to a particular file, and allowing the developers to see the changes from version to version.

Now imagine you’re working on a file and you create a bug. Version control allows for you to “rollback” your changes and remove that bug quickly so you can have time to repair it at a later time. Version control allows you to safely do that kind of operation.

The most common form of VCM is Git (not to be confused with [GitHub](https://github.com) which is a company that hosts git repositories). Git allows you to track files in your project, as you make changes to your files, git detects the changes made. Once you save your changes, you can use git to commit those changes and upload (or “push”) the changes to a central repository that other developers contributing to the same project also have access to. When another developer commits their changes, you can download (or “pull”) them into your local codebase. The “push” / “pull” process is referred to as a “merge” because that’s what git is doing. It only merges the changes to a file, it does not completely overwrite the file because that would mean any changes you made to that same file might be lost. Git does this by focusing on changes made on a line-by-line basis, so if you add an empty line, that would be a change hit would detect and consider a modification of the code.

In order for this process to work correctly we need a single source of truth for the codebase. One central location where developers can push and pull their changes to and from. This single source of truth is referred to as a “repository”. Typically a service is used to host the repository, and the most common of these is a website called GitHub. There are others, most notably [BitBucket](https://bitbucket.org), which essentially does the same thing. Regardless of which service is used, the steps to implement and use the repository are exactly the same. For this workshop, we will be using GitHub.

We’ll get into more specifics and features of git as we go, but for now, let’s take a closer look at VCM by providing a high-level example of how it is used in a typical project.

### Git Workflow Example

let’s assume you have a project that you’re working on and you have your local environment setup and the git version control software installed and integrated. Here’s a preview of how that version control will be a part of your overall workflow.

#### Begin your work session

When you’re ready to start working on the project, you’ll have a few very important steps to perform. First you’ll want to remember that other developers may have performed some work and updated the repository since your last contribution, so you’re going to always start things off by “pulling” down their changes and merging them into your local codebase. In practice that means you’ll open up a terminal application (more details on this to come when we talk about the actual tools you’ll be using) and executing a command to pull and merge the changes.

That would look something like this:

```Bash
$ cd <path/to/my-project>
$ git pull
```

> A couple of things to note about the above example code that will apply to all example code snippets moving forward in this workshop.

1. > The "$" is not the actual command, it's a shorthand to tell you that the actual command is meant to be executed in a terminal application. The "$" is the command prompt, so if you're copying and pasting the command, always leave out the "$" unless instructed to do so.
2. > The fact that we have two “$” indicates that we want you to execute two commands. You can think of it as telling you to execute the first command, then wait until you get another prompt from your terminal, then issue the next command.
3. > In the above example the commands we’re issuing are the next word (or words) immediately following the “$”. In our case, the two commands we’re issuing are `cd` and `git`, in that order. We’d like you to do the first one before the second one. The commands each take arguments (the details we want to provide for the command).
4. > In the above example, the `cd` command tells the terminal to change directory to the directory you provide after the command.
5. > The `<path/to/my-project>` is a placeholder, we're using the < and > to remind you to provide your path to your project. It is assumed that you would do so when issuing the command. Your actual command might looks something like this: `$ cd ~/Sites/example-site`
6. > The second command tells git to pull down any updates that you have not made and merge them into your project.

> If this is all sounding confusing to you, don’t worry, we’ll be getting into more specifics later on an you will be able to better understand all of this when put into practice.

#### Ready to Work + The Rest of the Workflow

After you pull down the changes and they’re merged into your project by issuing the commands in the previous step, you’re ready to begin your work. Let’s move a bit faster for the following steps.

1. Make your changes - any change would do here, this includes editing, adding, and/or removing files in your local codebase.
2. After you’ve reached a stopping point with your work session, you’ll want to go through the process of committing those changes.
3. Do another pull - because while you’re working another developer may have pushed up some of their changes as well. Doing a pull will ensure that you have the latest version of everything before you push up your changes. As a reminder the pull command is: `$ git pull`   If there are no updates, git will respond with a message: `Already up to date.`  If there are changes, those changes will be merged into your codebase and you'll have an opportunity to review them and fix any merge conflicts that may have occurred (more on this later)
4. Add your changes - next you will want to tell Git that you would like to add some changes. You do so by issuing a git command in your terminal tracking your changes: `$ git add .`  that command will tell git to add all of your untracked changes. We're using the `.` at the end to add everything, however you could replace that with a specific file if you wanted to only commit just that one file.
5. Commit your changes - at this point in the process your changes are still local. You’ll need to push them up to the repository as part of a commit so that other developers can pull them down into their codebases as well. To do this, we issue a `git` commit command, which takes one “message” argument expressed with a `-m “my message”` tag and commit message in quotes after the word commit. It looks like this: `$ git commit -m “my commit message”`
6. The “my commit message” would be a message you provide to summarize the changes you’ve made in this commit. These messages are very important to you and the other developers on the project because in the future you can refer to them to understand what changes made in that particular commit. In fact, git requires you provide a message and the commit command will fail if a message is not provided.

> A word of advice, don’t be lazy with your commit messages. Often times developers will put something generic or one non-descriptive word in their commit messages, only to regret it later when they wish to better understand the work that was done in that particular commit.

At this point, you’re done using VCM in your project. Your changes should be pushed up and ready to go. We’ve only scratched the surface on how version control works, but this is the flow you’ll be using throughout this workshop. It’s okay to not have a mastery, or even an understanding, of version control management at this point. Don’t worry, we’ll put this into practice repeatedly and often enough it will all start to make sense to you.

