# S3-06: Project Setup - TDD Setup

We discussed TDD in great detail earlier, now that we’re kicking off our project, we’re going to put this method into practice.

In TDD, we setup our tests first, then we write our code to get our tests to pass.

Let’s setup our testing suites.

1. Open the `Tempo` project in Visual Studio Code

![Screen Shot 2021-12-08 at 4.19.23 AM.png](S3-06:%20Project%20Setup%20-%20TDD%20Setup.assets/Screen%20Shot%202021-12-08%20at%204.19.23%20AM.png)

2. Open the file called `phpunit.xml` by clicking on it in the file explorer.

![Screen Shot 2021-12-08 at 4.19.52 AM.png](S3-06:%20Project%20Setup%20-%20TDD%20Setup.assets/Screen%20Shot%202021-12-08%20at%204.19.52%20AM.png)

You'll see two lines commented out (they appear in green and are on lines 24 and 25 in my screenshot.

3. Uncomment the two lines by removing the `<!—` and `—>` of each line.

This will allow our tests to a separate `sqlite` database for our tests. That way we can perform tests without impacting our actual development database.

Your file will look something like this when you’re done.

![Screen Shot 2021-12-08 at 4.23.20 AM.png](S3-06:%20Project%20Setup%20-%20TDD%20Setup.assets/Screen%20Shot%202021-12-08%20at%204.23.20%20AM.png)

That’s it, our project is all setup for our first test.

