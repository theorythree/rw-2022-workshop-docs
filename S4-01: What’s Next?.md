# S4-01: What’s Next?

With the initial client functionality built and testable, it’s time for us to move on to something entirely different. We’ll likely come back to clients, once we have more of our application built, but for now, we’re at a good stopping place with that particular feature set.

When deciding what to work on next, I like to look at our list of models that were put together in the project plane. Here they are for your review:

- > User*
- > Role
- > Project
- > TimeSheet
- > TimeEntry
- > HourlyRate
- > ~~Client~~
- > Team
- > Estimate
- > EstimateEntry
- > Invoice
- > InvoiceEntry

* User model is technically ready for us, but we do have some enhancements we need to make for us to apply authentication, roles, etc. For now, I’m going to pass on that one as a potential next effort.

The next logical one for us to work on will be the `Project` model. It’s very similar to the `Client` model and we can use this to our advantage.

Here’s what we came up with for the Project model in our project plan:

- > Project
   - > id (int | unique)
   - > client_id (int)
   - > name (string)
   - > description (text | optional)
   - > budget (float, 2 | optional)
   - > created_at (date time)
   - > updated_at (date time)

You can see that there are only a few minor differences between Client and Project. The most notable difference is that we have a client_id in this model which is meant to link it to a specific client. This will give us a chance to discuss how relationships work in a Laravel project. So that’s something to look forward to.

Beyond those two new things, we’ll take on the same development strategy by maintaining our approach of TDD, writing our tests first, then create our routes, update our controller, then create any dummy views we’ll need to show our final work.

Sound good? Great, let’s get hopping.

