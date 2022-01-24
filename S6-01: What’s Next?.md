# S6-01: What’s Next?

We’re getting to the important features that will make our time-tracking app an actual time-tracking app. We're going to work on three more models in this series; TimeSheet and TimeEntry. These two models relate to one another and so tackling them both at once will be the best approach.

> The goal of this series will be to build migrations, tests, models, routes, controllers and the appropriate views for  time sheets and time entries.

Here’s our master project checklist updated with the work we’ve completed thus far:

- > ~~User~~
- > ~~Role~~
- > ~~Project~~
- > TimeSheet
- > TimeEntry
- > HourlyRate
- > ~~Client~~
- > Team
- > Estimate
- > EstimateEntry
- > Invoice
- > InvoiceEntry

Here’s how we defined these models in our [[S3-01: Project Planning (Models)]]

- > TimeSheet
   - > id (int | unique)
   - > user_id (int)
   - > date (date)
   - > created_at (date time)
   - > updated_at (date time)
- > TimeEntry
   - > id (int | unique)
   - > project_id (int)
   - > invoice_id (int)
   - > time_sheet_id (int)
   - > user_id (int)
   - > duration (int)
   - > invoiced (bool)
   - > created_at (date time)
   - > updated_at (date time)

   Okay, with our goal clearly defined, I’ll see ya in the next episode where we’ll start working on TimeEntry and TimeSheet.

