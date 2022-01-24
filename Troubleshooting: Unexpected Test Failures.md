# Troubleshooting: Unexpected Test Failures

### Troubleshooting Test Unexpected Test Failures

If you see a test fail for you and not me, that would be a good time for you to do some debugging. First, isolate the test that is failing for you by filtering just it. So, for example, if you saw a fail on `test_user_can_create_a_project()` you’d run:

```other
sail artisan test --filter=test_user_can_create_a_project
```

Read the error output very closely, it should guide you to the place in your code where the error exists. Maybe you’re getting a parse error because you missed an open or close curly bracket `{` or  `}`? Maybe you forgot a semi-colon `;` ? Maybe your migrations didn't run because you have the definitions incorrectly coded? Perhaps your route isn't working correctly? (you'd see a 404 or some kind of routing error if that were the case). Maybe your issue is with the test itself? Can you verify that the test is testing what it's supposed to be testing?

Finally - refer to the session where we performed the work on the file(s) you’re having issues. Make sure your code exactly matches mine. You’ll get it and this is good practice for you to start to understand how all the pieces come together.

