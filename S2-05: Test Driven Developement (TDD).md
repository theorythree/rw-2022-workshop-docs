# S2-05: Test Driven Developement (TDD)

Test-Driven Development is a process where a developer will write a series of test before writing any code to make those tests pass. For most developers, writing tests typically happens after the code has been written and the application is about to ship or already shipped. This turns that thinking on its head and aims to change that way of thinking.

### What you'll hate about TDD

If you’re coming from the other side of this, you may not even have written a single test at all. The idea of writing tests seems like a bit of a waste of your time. After all it’s just one more set of code that you have to write and maintain in your project, right?

Valid point.

Another complaint I hear all the time is the process itself can be somewhat daunting. After you do write your tests, you have to run them repeatedly, each time seeing your tests fail over and over again before you finally get that green pass response you so desperately longed for when you first wrote that test.

Another valid point.

### What you'll love about TDD

After you get a few tests written and under your belt, you’ll start to see the value of all your hard work.

1. Write less code The first advantage you’ll see is you’re going to be writing less code than you think. Tests are designed to be short succinct and singularly focused in nature. You’re only testing one aspect of one feature, so you’re only going to be writing a few lines of testing code. Also, writing good test code means you’ll end up writing far less actual code in your project. TDD motivates you to write only what you need to pass your test. As you add tests, you add more code and simultaneously continue to test the code you’ve already written to make sure it still works. This means as you build you naturally avoid all the cruft that typically gets added to your codebase.
2. Expose bad patterns in your coding technique. TDD will also help you identify bad patterns that you may have picked up throughout your years of coding, or perhaps because you’re new and don’t know any better (we’ve all been there). Testing small portions of your code a piece at a time, means you’re going to write code that is more focused and has a single purpose. This is because often times one test will test against one feature or even one method in your codebase.
3. Far far far less bugs. If you introduce TDD into your development workflow, you’re going to ship with less bugs. Testing your code means that you avoid testing in the way you probably are now. As your application code becomes more and more complex, using your current method, you’re bound to introduce a bug here and there. Especially in cases when your working on a new feature in your application and that work impacts an older feature you may have coded months ago.
4. Far easier to refactor your code. As you grow as a developer, you’re going to have a moment in your career that you get a chance to look back at some the code you wrote years ago. Such an event can be quite painful. You’ll find yourself asking “what was I thinking when I wrote this?”. You’ll think about refactoring your code to make it more efficient, more readable, more succinct perhaps. But then you’ll remember that this particular piece of code is pretty complex and if you refactor you might break one of the many features that your application depends on. It’s working after all, so is it really worth it? With a proper test suite in place, you can go ahead and scratch that itch to clean things up. All you have to do is run your tests while you work to verify that everything returns to green when you’re done.
5. Spend less time debugging. Imagine being able to test every feature of your application all with in a few seconds. How long would that take you if you had to test your application now. Perhaps you’d have to setup a couple of test users and run through each feature in your app a couple of times to make sure all is still well. Bugs are bugs because they’re small, and they impact your application in very specific ways. Perhaps if a user of your application logs in then hits their profile page and then decides to place an order, their order items disappear from the shopping cart. With a proper testing suite you can test that exact scenario anytime you run your test. Pretty cool.

In short, expect to hate it before you love it. Just keep reminding yourself that this will payoff in the long run and eventually you’ll come to love it.

### But how does it work?!?

Simple. It all starts with a test. Let’s say you’re starting up a project, a blog is a good example. Your blog is going to have users, each with different roles (users, authors, administrators, etc). Some of the users can create blog entries, others are just there to read, still others are able to administrate the other users and control when those blog posts get published. The dynamic of controlling all the possible combinations can get confusing just describing it.

But with TDD, we probably would start with the blog entries themselves. We’d start with just creating a blog post. We’d write a test, say something called `test_user_can_create_blog_post()`, then we’d write that test before anything else.

It might look something like this:

```php
public function test_user_can_create_a_blog_post()
{
  // create a blog post in the test database
  $response = $this->post('/posts', ['title' => 'TDD, and How it Changed My Life', 'content' => 'TDD is the best thing since...']);
  // assert that we have one post in the posts database table
  $this->assertTrue(Post::all()->count() == 1);
}
```

Running such a test right away will fail.

But the error we get from that failed test will guide us to the work we have to do. That’s the point with TDD.

In our example our first tests might guide us to create a model called `Post`, then we’re going to need a `database migration` to create a table called `posts`. Further errors of our will guide us to create a controller (`PostController`) that will handle the saving of our blog entry to the database. Oh, and then we're going to need to create a `route` (url: /posts) in our web.php routes file that manages the routing of our request. And so on…

### But I know all that, do I really need to write a test to tell me what I already know?

In short, yes, well, kinda.

You may know all that, but getting into the habit of testing as you go will confirm you’re test is indeed testing all the potential failing points in your process. It’s a reminder to do all the tasks every time, and until your test passes. It builds confidence in your process, so you don’t skip any steps and move on to the next task.

As you become more comfortable and confident in your coding skills, you might not take such a fine-grained approach to your testing method. You might decide to write several tests; one for creating a blog entry, one for editing, one for viewing, and for deleting a blog entry. All of those standard CRUD methods, could be all knocked out at once if you were so inclined.

### Final Thoughts

There are a few warnings I’ll leave you with as you decide if this methodology is right for you. You have to go all in, don’t write a few tests that don’t have “good coverage” of your codebase and expect to see the advantages.

If you’re on a team that tests, then you have to test or you’ll be undermining all their hard work.

TDD is not a silver bullet. It’s not going to make your codebase 100% bug free, nor is it going to solve all your issues. You can write bad tests that give you a false positive sense of confidence in your codebase and that, my friend, can be equally damning as anything else.

Finally, start small, test small. Don’t try to take on too much with your testing all at once. Start with your core features, test there. That way you’ll at least know that the future contributions to your project won’t break what makes your app so special to the users that use it.

Good luck, happy testing.

