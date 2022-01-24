# Tools: Customizing Visual Studio Code

One of the defining advantages to using Visual Studio Code (VS Code for short) is the vast number of ways you can customize the program.

I would divide the customize options into two main categories. Theming and Extensions.

Theming refers to the visual look and feel of the editor, while the extensions tend to do just that, extend, the functionality of the editor so you can perform some task more efficiently within the editor (think of everything from simple code completion to a full REST API editor right within the program).

VS Code has a built in marketplace for all extensions which means you can find and install any extension or theme right from the Extensions icon on the far left of the main window. (VS Code refers to the icons in this space as Actions icons).

### Theming

Theming is a great place to start customizing VS Code, because it won’t break anything in your setup. You can simply swap out one theme for another and call it a day. As you become more and more opinionated you’ll start to find you favor some theming styles over others.

I personally prefer dark themes with minimal color options. I find the rainbow of colors completely distracting and difficult to read. I also prefer my icons and folders to be minimal as well for the same reason.

I purposefully chose to use the default dark theme that came with VS Code so that I would not confuse you, the learner, with a setup that looked entirely different from your own.

Here’s how I achieved the theme you’ve seen in the screenshots in this workshop. Feel free to follow along if you want to match.

1. Open VS Code
2. Open the Command Palette by clicking `Command` + `Shift` + `P` (`Control` + `Shift` + `P` on a PC) and type the following:

```other
Preferences Color Theme
```

You should see one option, click on it to open another set of menu items

![Screen Shot 2021-12-18 at 2.54.57 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%202.54.57%20PM.png)

Your menu will look different than mine, because I have some additional themes installed already.

3. Choose the Dark `(Visual Studio)` option to switch to that theme

The next thing I like to do is use the set the file icon theme to minimal

1. Open the Command Palette again and type

```other
Preferences File Icon Theme
```

2. Choose Minimal (Visual Studio Code)

![Screen Shot 2021-12-18 at 2.59.40 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%202.59.40%20PM.png)

That’s it, that is the identical theme I used for this workshop.

### Installing a Theme

Installing a theme is pretty straightforward.

1. Click on the Extensions action icon on the left side of the main window.

![Screen Shot 2021-12-18 at 3.02.57 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%203.02.57%20PM.png)

Again, my extensions will look different than yours, but you should see all the installed extensions you have. Themes are packaged up as extensions that change the style values of your editor when installed and enabled.

In the Search Extensions in Marketplace search for Horizon Theme. You’ll see I already have it installed, but installing and uninstalling happens with a click of a button.

Install Horizon Theme

![Screen Shot 2021-12-18 at 3.06.11 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%203.06.11%20PM.png)

Once installed, you can find it listed in the Command Palette by typing the following search term as we did before.

```other
Preferences Color Theme
```

Horizon Bold is my favorite theme at the moment. Click it and open up any code file in VS Code to see the styles this theme provides.

![Screen Shot 2021-12-18 at 3.10.45 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%203.10.45%20PM.png)

Feel free to change it back to the default theme by repeating the steps above.

---

### Extensions

Extensions can enhance virtually any aspect of the editor and how it behaves and functions.

> Just one word of caution with extensions, try not to go hog wild and install everything all at once. Doing so will make it difficult to incorporate the extensions into your workflow. I highly recommend installing one or two at a time and then, once you’ve become comfortable with those extensions install some more. I can’t count the number of times I’ve searched for an extension because I want to do a particular thing and I already had it installed and I just wasn’t using it.

Here are a few to start you off right.

You can install all of these by clicking on the Extensions action icon and searching the extensions marketplace for the name of the extension.

### Todo Tree

![Screen Shot 2021-12-18 at 3.52.07 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%203.52.07%20PM.png)

> This one is a project management must for me. In short, all you have to do is put a TODO comment anywhere in your code and this little guy will create a handy list linking you to that exact spot in your code. I use TODOs in all my projects and they can be a great way to remain focused on the task at hand because you know you’ll be able to come back and refactor that code later.

### Laravel Blade Snippets

![Screen Shot 2021-12-18 at 3.55.15 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%203.55.15%20PM.png)

> This is a handy little extension that allows you to code-complete some things in your Blade templates. It can save on coding in Blade and help prevent those pesky typos that happen from time to time.

### Indent-Rainbow

![Screen Shot 2021-12-18 at 3.57.43 PM.png](Tools:%20Customizing%20Visual%20Studio%20Code.assets/Screen%20Shot%202021-12-18%20at%203.57.43%20PM.png)

> This plugin helps keep my code properly indented. It has one simple job, to colorize the indentation in front of your lines of code. I’m a visual person so I prefer this kind of plugin over the ones that attempt to auto format your indentations.

That’s it for now, I may pull together a full list of extensions and theme mods in the future, but for me, it’s time to get back to work.

