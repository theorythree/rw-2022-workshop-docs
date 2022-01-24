# S2-01: Exploring Visual Studio Code

In the last series we went through the process of setting up your local environment and a test Laravel project we called `s1-laravel-project`. Let’s use that project to explore and get comfortable with our IDE (Visual Studio Code)

1. Open up the Laravel project in Visual Studio Code that we created in the last series. Your screen should look something like this:

![Screen Shot 2021-11-29 at 11.53.19 AM.png](S2-01:%20Exploring%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-11-29%20at%2011.53.19%20AM.png)

### The App Icons

![Screen Shot 2021-11-29 at 11.56.31 AM.png](S2-01:%20Exploring%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-11-29%20at%2011.56.31%20AM.png)

On the left of your screen you’ll see the app icons, these will switch Visual Studio Code into the different modes, the default is the file explorer, which will use to explore our project files in this section. Don’t worry if your icons do not exactly match up to mine, I have some additional plugins installed. The main app icons will be the same for you and we’ll talk about the others later in this workshop.

### The File Explorer

![Screen Shot 2021-11-29 at 12.00.28 PM.png](S2-01:%20Exploring%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-11-29%20at%2012.00.28%20PM.png)

The file explorer will show you all of the files in our project. You're looking at the project root folder and all of the files and folder in it. This is also the folder that Visual Studio Code will default to in any terminal windows you open up while in this project. To open a file all you have to do is click on it and it will open up in the editor window pane.

Let’s do that now. Open the following file:

> /app/Models/User.php

You’ll notice the file opens up in tab in the editor pane and this is where you’ll do all of your work on the file before closing it.

![Screen Shot 2021-11-29 at 12.17.54 PM.png](S2-01:%20Exploring%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-11-29%20at%2012.17.54%20PM.png)

Visual Studio also has the ability to add, rename, and delete files and folders. More on that to come. for now, just take a peek at this file and notice a few things.

1. The code has syntax coloring by default. The coloring will help keep everything organized and recognizable by applying certain colors to your code as you start to write it. The coloring is also different based on the file, in our case we’re opening a `php` file and Visual Studio knows that keywords like `namespace` and `use` are different and need to be set apart from the code that proceeds it.
2. It opened the file in a tab, which can be seen in the top portion of the window pane. Tabs work just like other programs. You can have multiple tabs open and switch between them as needed.
3. The code has a column of numbers on the far left of the window pane, which we will refer to as `line numbers`, which will help identify specific lines of our code in the future.

> You may notice my editor vertical color bars to help make the indentations in this file easier to determine. This is a Visual Studio plugin I use to help with that task.

> Making your code readable is very important and we’ll discuss this in more detail in a future topic that covers coding conventions and coding style guides.

