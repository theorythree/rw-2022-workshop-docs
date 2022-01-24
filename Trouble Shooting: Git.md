# Trouble Shooting: Git

While putting this workshop together, I identified several spots along the way that you may run into some trouble. I decided to collect them all into this section so you could refer to them as needed.

## Trouble Shooting Git

Here are some handy get out of trouble uses cases that you might find useful if you have issues with the git process.

### How to remove tracking on a file you did not mean to modify

For review, committing your changes looks like this:

```Bash
$ git status
$ git pull
$ git status
$ git add .
$ git commit -m "commit message"
$ git push
```

> If, on the first command, you see a file listed that you did not mean to modify, do NOT continue adding and committing that file. Even if, for example , you just added a line or some other non-destructive change, you never want to check that change in by accident. This is because you’ll be saying your commit meant to change this file, and that could be quite confusing to someone in the future if your changes had nothing to do with the intent of your commit.

So, for example, if you saw User.php listed among the files when you checked the status and you didn’t want to commit your changes to that file, you’d remove it.

Here’s how.

1. Run a status check one more time to verify the unwanted file is indeed changed

```other
$ git status
```

![Screen Shot 2021-12-12 at 1.14.06 PM.png](Trouble%20Shooting:%20Git.assets/Screen%20Shot%202021-12-12%20at%201.14.06%20PM.png)

> You can see in my case, I modified the `User.php` file for demonstration purposes, and I do want to commit all of the other files in the list.

2. Revert any changes you may have made in the unwanted file (in my case User.php) by issuing the following command.

```other
$ git checkout app/Models/User.php
```

> You’ll want to replace the path and file part of your command to match your unwanted file.

![Screen Shot 2021-12-12 at 1.18.15 PM.png](Trouble%20Shooting:%20Git.assets/Screen%20Shot%202021-12-12%20at%201.18.15%20PM.png)

You’ll notice that you get a message that 1 path from index was updated.

3. Run another status check to verify we've successfully removed our unwanted file.

![Screen Shot 2021-12-12 at 1.19.56 PM.png](Trouble%20Shooting:%20Git.assets/Screen%20Shot%202021-12-12%20at%201.19.56%20PM.png)

You can see from my example `User.php` was removed. Which is what I wanted.

Repeat these steps until you have removed all the unwanted files from the list of changed files, then it’s safe to continue with the rest of the commit steps.

Alternatively, if you wish to remove ALL of the listed files and destroy the changes you can do so by using the . instead of the file paths and names individually.

```Bash
$ git checkout .
```

