# S3-05: Project Setup

It’s finally time to start rolling up our sleeves and begin the process of actually building out our project. Executing our plan should be fairly straight forward.

One final detail is to name our project. I’ve chose to name this project `Tempo` which is Italian for the word `Time`.

With that final detail out of the way, let’s begin by setting up our project.

### Steps

1. In the terminal, navigate to your web root folder and issue the following command

```Bash
$ curl -s "https://laravel.build/tempo" | bash
```

> You may have to enter you computer admin password for the setup process.

2. Change into your project directory and boot up your project with the Sail command we learned about earlier.

```Bash
$ cd tempo
$ sail up
```

> > Be sure to have Docker Desktop is running before you issue the Sail command.

> > Remember the first time you issue the `sail up` command it will take some time to spin up your project. Be patient.

1. Leave the terminal window open to keep the services running.
2. In a browser, type in `localhost` in the address field. If all goes well you should see the Laravel welcome screen.

![Screen Shot 2021-12-08 at 4.04.47 AM.png](S3-05:%20Project%20Setup.assets/Screen%20Shot%202021-12-08%20at%204.04.47%20AM.png)

