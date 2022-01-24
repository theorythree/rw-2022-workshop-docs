# S7-01: What’s Next?

Another series down, as is tradition, we’ll ask that important questions, what’s next?

I want to take a moment and discuss a very important part of developing a project such as the time tracker application we’re currently developing. And that’s how we handle change in project scope and requirements.

If we lived in a perfect world, our project plan would be locked in and we’d go on developing our code exactly as planned out. However, in the real world that rarely happens. So I thought it would be good practice to reevaluate our project and see if there are things we want to add, remove or change. In doing so we can create our own examples of how to pivot while in development.

Here’s a review from the [[S3-01: Project Planning (Models)]] episode updated to the current status.

- > ~~User~~
- > ~~Role~~
- > ~~Project~~
- > ~~TimeSheet~~
- > ~~TimeEntry~~
- > HourlyRate
- > ~~Client~~
- > Team
- > Estimate
- > EstimateEntry
- > Invoice
- > InvoiceEntry

I want to identify the items that we wish to remove from our list permanently, or to delay their development, items that we want to add, and finally, items we might want to enhance from our original plan.

Here’s the list I came up with.

### Remove: Team

I do not want to use teams as we had originally thought of them. In fact, we may drop teams entirely. What makes more sense is the use of project roles. For example for each project you could define a person as a developer, another as the writer, and another user as the person responsible for QA. Each of these roles would be defined for each new project. This kind of setup is quite common with teams of all sizes.

### Add: Company

I want this time tracker to be sold to companies. Companies will have their own members (users) who will only be visible by the owner of that company.

### Phase 2: Invoice & Estimates

This feature is a nice to have, and isn’t really part of our minimum viable product (MVP). True, we need these things, but they do not have to be available for our first release of the project.

### Enhance: Time Entry

Right now there’s just one enhancement I want to make to time entry, and that’s the inclusion of a date field. My original plan was to use the time sheet date and/or the created_at field, but I want the user to be able to add time entries for dates other than today.

Here’s our updated project plan, not too far off from the original.

- > ~~User~~
- > ~~Role~~
- > ~~Project~~
- > ~~TimeSheet~~
- > ~~TimeEntry~~
- > HourlyRate
- > ~~Client~~
- > Company

### Phase 2

- > Estimate
- > EstimateEntry
- > Invoice
- > InvoiceEntry

