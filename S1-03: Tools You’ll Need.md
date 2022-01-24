# S1-03: Tools You’ll Need

We’re ready to start setting the project up. In order to get started, we first need to setup your local environment for development. This process can be somewhat of a pain point for most developers, even advanced developers who are very familiar with all the technologies in use can struggle to setup their local environments at times.

There are essentially two main approaches, the first being that you install and manage all of the technologies in use in your project on your own. There are tools that help make this process easier than it may sound, but you’ll essentially be on your own, and any incompatibilities between the software you install and those on your team will have to be managed and resolved by you.

The second approach is to use what’s called a “container” for your project. This container can be shared across your team, which has the same installed versions of software and configurations needed to serve up the site in the same way for everyone.

For the sake of simplicity, we will be going with the second option for this workshop. We will be using a popular container called [Docker](https://www.docker.com), but you should know this is a personal preference and some of your teammates may choose to setup their local environments differently and it will ultimately have no affect on the final outcome of the websites we will be building.

### Tools

It’s time to decide what tools you’ll be using to do your work. There is nothing more personal to a developer than the tools he/she/they decide to use, so, while I will be recommending certain tools for you to use, you may decide to use an alternative that better fits your personal preference.

> If you’re new to development, I would highly suggest following along with my recommended tools because I will be sharing screenshots and if your software matches mine, you’ll have an easier time following along. Once you master these concepts and begin to do your own projects outside of this workshop, you can feel free to replace the software with something you prefer to use. After all you’re going to be spending a lot of time using these tools and it’s important you work with tools that best fit your personal preferences.

### Computer

It may seem obvious, but you’re going to need a computer to participate in this workshop. If you’re wondering what type of computer is best, you’re in good luck. The minimum requirements are pretty, well, minimum. Your computer just needs to be able to open and edit text files, connect to the internet and use a modern web browser. All pretty standard stuff when it comes to a computer. I personally, prefer a Mac, however Windows and Chromebook machines are equally capable of doing all of the development work we will do in this workshop. All of the software we will use is available on all of the computers I mentioned, so following along should be easy. In the rare case the type of computer you use impacts anything, I will be sure to mention that at the time.

#### Software: IDE

An IDE is short for Integrated Development Environment, and it’s just a fancy word for a text editor. Not all text editors are the same, and a well-built IDE will make life a whole lot easier for you. Any good IDE worth their salt will have some features including syntax highlighting for a variety of languages, some kind of version control integration, and advanced formatting capabilities. The more advanced IDEs have features that help with the productivity of your coding by providing error checking while you code, linking to resource files and documentation support for the languages you'll be coding in.

For this workshop, we will be using Microsoft (I know) Visual Studio Code. It more than satisfies all of our basic requirements and has become the go-to IDE for the more seasoned developers. It supports a wide variety of languages (including all the web development languages we will be using) and has a vast ecosystem of plugins which can be installed to customize the software and make your life a lot easier.

You can [download Visual Studio Code](https://code.visualstudio.com/download) and the user interface will be the same no matter what computer you use.

If you’re used to web development and you’re interested in checking out alternatives, I’ve used [Atom](https://atom.io), [BBEdit](https://www.barebones.com), [Eclipse](https://www.eclipse.org/ide/), and [PhPStorm](https://www.jetbrains.com/phpstorm/promo/?source=google&medium=cpc&campaign=14335686138&gclid=EAIaIQobChMI0JDM3-S79AIVMXxvBB0bow9EEAAYASAAEgKRI_D_BwE), which all work well enough for the type of web development.

#### Software: Browser

The browser you use for development is another personal preference. As I mentioned, I’m on a Mac, so I prefer to use Safari, only because there is a well-known memory leak on Chrome that Google refuses to address. In any case, you need one main browser that allows for you to inspect the contents of a webpage, and nearly all modern-day browsers allow you to do so. In fact Chrome and Safari have nearly identical web tools for this, so my recommendation is to use Chrome if you’re on a Linux or Windows machine and Chrome or Safari if you’re on a Mac.

#### Software: Docker

I already mentioned that we will be using Docker for this workshop. It essentially removes the need to talk about and setup all the various technologies we will be using in this workshop (PHP, Composer, MySQL, Node, NVM, etc). Instead of spending time installing and configuring all of those softwares, we’ll opt to install Docker and leverage the fact that all Laravel projects are setup to work with Docker natively. In fact, in using it, we don’t even have to worry about what type of computer you’re using in this workshop, as the various technologies, and specific versions of those technologies, will be all bundled up within the project container. Simple and neat. Just like we like it.

You can [download Docker](https://docs.docker.com/get-docker/) and install and use it no matter what type of computer you’re using.

#### Software: Database Client

We are going to be doing some work in a database, and while we can do all of that work in the command line, ideally we would use a database client that has a GUI (graphic user interface). It will make our lives a lot easier when we’re doing this work if we can switch database tables and look at the actual data.

My favorite database client is [TablePlus](https://tableplus.com) it works on both Windows and Mac, and offers a free pricing tier that will work perfect for our needs.

### Software: Git

We’re going to be using Git for version control management of our code in this workshop. You may need to install it on your computer, if you haven’t already.

If you’re not sure if you have it already installed, open a terminal window and issue the following command:

```Bash
$ git --version
```

You should see some kind of output with a version number if you do have it installed.

![Screen Shot 2021-12-12 at 12.07.59 PM.png](S1-03:%20Tools%20You%E2%80%99ll%20Need.assets/Screen%20Shot%202021-12-12%20at%2012.07.59%20PM.png)

If you’re on a Mac and Git is not installed, you will be prompted to install it.

Here are some instructions on how to install Git for your particular operating system.

[Getting Started - Installing Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

> Avoid the installation instructions that have you installing from source. Those instructions are for users who want to customize the install in very specific and advanced ways.

