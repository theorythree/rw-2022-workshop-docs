# S3-07: Our First Commit

As I mentioned we’re going to be using version control software to check in our code as we build this project. You may be asking when is a good time to check in your code? The answer is early and often. When you first setup your project, and before you get too far in development, you’ll want to do your initial commit of your code.

The reason for this is we want to capture the current state of the code while everything is working properly so if we need to roll back to a point in time we have a good spot to do so.

### You're going to need a GitHub account

> You’re going to need a GitHub account to follow along with these next steps, if you do not already have one. Don’t worry the account is free, and a free account will allow you to create and use an unlimited number of public and privet repositories.

> Check out the the [feature comparison page on GitHub](https://github.com/pricing#compare-features) to learn more about the advantages of paid plans. If you're just starting out, you really do not need a paid plan, so don't feel like you'll miss any meaningful benefits if you decide to go with the free plan.

Once you create your account you’ll have an account “handle” which is the username you provided when setting up your account. Your handle will be used quite a bit on GitHub, it serves as your user login, and it will be asked of you if you want to be invited to join another developer’s repo, or team. So if you’re creating a GitHub account, try to pick something professional. I chose the first letter, last name convention for my handle (dmerfeld).

> You can view my public profile here: [github.com/dmerfeld](https://github.com/dmerfeld)

> And my company’s profile here: [github.com/theorythree](https://github.com/theorythree)

### Create a Repository

Now that you have an account, visit your GitHub page `https://github.com/{your handle}` and  look for the `New` button (shown in green on my screenshot below).

![Screen Shot 2021-12-12 at 11.07.37 AM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2011.07.37%20AM.png)

Your button may appear in a slightly different location depending on what type of account you have.

1. Click the `New` button

![Screen Shot 2021-12-12 at 11.21.50 AM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2011.21.50%20AM.png)

2. Name your repository: `tempo` Each of your repositories must be uniquely named, but can have the same name as another person’s repository. So unless you already have a project named `tempo`, name it that.  Names should be Kebab Case (lowercase no spaces between words)  As you type your repo name, GitHub will confirm that it's available.
3. Add a description: `A simple time tracking app` This is an optional step, but recommended. As you added more repos to your account you might want a short description of what this project is all about.
4. Make your repo public or private Repos can be public (Anyone on the internet can see this repository. You choose who can commit.) or private (You choose who can see and commit to this repository.)  I’m going to make my repo public, because I want to open source this project and make it available to anyone who wants to contribute to it. You may do as you wish. If you’re unsure, just make it private and you can always make it public if you change your mind later.
5. Initialize your repo with some starter files We really do not need to do this step, so we’re going to skip it, but I wanted to let you know what each of these files is and why GitHub is recommending we consider including them in our project code.
   1. README file: This file is already in our Laravel project file and it’s a standard file included in nearly all software. The purpose of the file is to instruct users of our code how to setup the project, and a few other top-level details on how to use the software we’re going to create.
   2. .gitignore file: This file tells Git what files should not be tracked so sensitive information does not get included in our repository (for example, database names and passwords). Again, our Laravel project already has one of these files and it already has all the important ignored files specified.
   3. License: Licenses explain how users may use our software. You may safely ignore this for most of your repositories, unless you plan on making your software publicly available for people to use and install. Again, you can skip this one.

Here’s what your settings should look like when you're done, (except for the `owner` which for me is `theorythree` and for you will be your GitHub handle).

![Screen Shot 2021-12-12 at 11.48.33 AM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2011.48.33%20AM.png)

6. Click the Create repository button to finish up.

![Screen Shot 2021-12-12 at 11.51.17 AM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2011.51.17%20AM.png)

GitHub provides us some options for our next steps. I won’t cover them all, but they are written to help us connect an existing project, create a brand new empty project or even sync up an existing repo to the one we just created. We’re not going to do any of that. We already have some project code so all we need to do is initialize git on our project code and push it up. It’ll be super simple.

### Initialize and setup Git on our project

1. Keep your browser window open, we're going to need that https url later
2. Open the `Tempo` project in `Visual Studio Code`
3. Open up a terminal window (you can use the keyboard shortcut `control` + ```) and type in the following command which will initialize a git repository for us.

```Bash
$ git init
```

![Screen Shot 2021-12-12 at 12.12.25 PM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2012.12.25%20PM.png)

> If you get an error saying git is an unknown command, then you do not have Git installed on your computer. Follow the instructions here:  [[S1-03: Tools You’ll Need]]

You should see a message telling you initialized an empty repository most of your files in the file explorer will turn green, which tells you they’re new files that have not been committed in your repository before.

4. Make your initial commit.

```Bash
$ git add .
$ git commit -m "initial commit"
```

You should see a list of files (nearly all the files in our project) and Visual Studio will change the files from green to white (or black if you’re using a light theme).

![Screen Shot 2021-12-12 at 12.16.58 PM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2012.16.58%20PM.png)

5. Connect your blank repository (with your initial commit) to the one we created earlier by defining the default branch name main and the remote origin url.

```Bash
$ git branch -M main
$ git remote add origin https://github.com/{your-github-handle}/tempo.git
```

> Be sure to replace `{your-github-handle}` with your GitHub handle that matches your GitHub profile username in the second command. You can get your specific URL from the browser window I had you keep open in the first step.

![Screen Shot 2021-12-12 at 12.22.25 PM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2012.22.25%20PM.png)

6. Push your initial commit and set your upstream for your initial commit

```Bash
$ git push --set-upstream origin main
```

![Screen Shot 2021-12-12 at 12.26.12 PM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2012.26.12%20PM.png)

> You may notice in my screenshot, I first attempted to push the commit using just `$ git push` and Git gave me the correct command I needed (`git push --set-upstream origin main` ) to use to actually do the first commit. This is common for me, I’m so used to typing `$ git push` on a repository that already has this setting configured. And now that we've set the upstream, we can use just `$ git push` from now on.

Your initial commit should now be pushed up to GitHub. You can verify this by refreshing that browser window I had you keep open in step one. You should see your project code listed on the page.

![Screen Shot 2021-12-12 at 12.35.22 PM.png](S3-07:%20Our%20First%20Commit.assets/Screen%20Shot%202021-12-12%20at%2012.35.22%20PM.png)

> If you closed your browser window for some reason, you can find your code using the following address.

```other
https://github.com/{your-github-handle}/tempo
```

### Initial commit committed, now what?

We will be making many many more commits in this project, so for now, we can move on to the task of building our project code. Along the way, I will include the commands we need to pull, add, commit, and push our code to GitHub.

For now, commit (you see what I did there?) to patting yourself on the back. You just used version control to commit your code. Now you have a backup of where your code is at this point in time and you can continue to add to the project knowing you can always regress back here if you need to in the future.

