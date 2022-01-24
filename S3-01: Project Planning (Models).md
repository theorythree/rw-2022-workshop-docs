# S3-01: Project Planning (Models)

We are going to be building a time tracker application. Before we dive in to setting up our project we should do some project planning. It’s very important that we define what our application features are going to be and how the various parts of our application will relate to one another.

Let’s start of by defining what the various “things” will be that belong to our application. We can do so by saying our application will have… and then listing our responses.

These “things” will eventually become our models, so it helps to think of them as sets of data we’ll need to keep track of in our application database.

### Our application will have…

- > Users
- > Roles
- > Projects
- > Time Sheets
- > Time Entries
- > Hourly Rates
- > Clients
- > Teams
- > Estimates
- > Estimate Entries
- > Invoices
- > Invoice Entries

I’ve taken great care to name these things in a particular way that sets us up to define what will become our data models. All we have to do now is to name them appropriately to the naming convention we should adhere to for all our models. Single tense and camel-cased.

### Our Project Models

- > User
- > Role
- > Project
- > TimeSheet
- > TimeEntry
- > HourlyRate
- > Client
- > Team
- > Estimate
- > EstimateEntry
- > Invoice
- > InvoiceEntry

> > This may look a bit strange at first, but it’s helpful if you think of Models as a single occurrence of the thing it represents. The model refers to a single `User` not a collection of users.

### Model Relationships

Now that we have our list of models, we should determine the relationships between them all. To do that I just run down the list and ask myself does this particular model have a relationship with any other model? If so is it a `one-to-one`, a `one-to-many`, or a `many-to-many` relationship? The relationship type will become obvious when we run through them all.

> User

   - > can have many roles
   - > can be working on multiple projects
   - > can have multiple time sheets
   - > can have multiple time entries
   - > can be working on multiple clients
   - > can belong to multiple teams
   - > can author multiple estimates
   - > can author multiple invoices
   - > can generate multiple reports

> Role

   - > can have multiple users belong to a role

> Project

   - > can have multiple users working on a project
   - > belongs to a client

> TimeSheet

   - > can have one user
   - > can have multiple time entries
   - > can belong to one date

> TimeEntry

   - > can belong to one user
   - > can belong to one project
   - > can belong to one time sheet
   - > can have one hourly rate
   - > can have one client
   - > can have one invoice
   - > can belong to one date

> HourlyRate

   - > can belong to many users
   - > can belong to many clients
   - > can belong to many estimates
   - > can belong to many invoices

> Client

   - > can have multiple users working for them
   - > can have multiple projects
   - > can appear on multiple time entries
   - > can have multiple estimates
   - > can have multiple invoices

> Team

   - > can have multiple users in it

> Estimate

   - > can have one author (user)
   - > can have one contact (user)
   - > can have one client

> EstimateEntry

   - > can have one author (user)
   - > can have one client

> Invoice

   - > can have one author (user)
   - > can have one contact (user)
   - > can have one client

> InvoiceEntry

   - > can have one author (user)
   - > can have one client

### Model Attributes (Database Migrations)

The final stage for our models would be to define the attributes for each of our model types. Each attribute will represent a column in the database for that Model. I like to also include the data type in parentheses as well.  These attribute definitions will become very useful when we setup our `database migrations`.

- > User
   - > id (int | unique)
   - > name (string)
   - > email (string | unique)
   - > title (string | optional)
   - > password (string | encrypted)
   - > active (bool)
   - > created_at (date time)
   - > updated_at (date time)
- > Role
   - > id (int | unique)
   - > name (string)
   - > display_name (string)
   - > description (text | optional)
   - > created_at (date time)
   - > updated_at (date time)
- > Project
   - > id (int | unique)
   - > client_id (int)
   - > name (string)
   - > description (text | optional)
   - > budget (float, 2)
   - > created_at (date time)
   - > updated_at (date time)
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
- > HourlyRate
   - > id (int | unique)
   - > rate (float, 2)
   - > created_at (date time)
   - > updated_at (date time)
- > Client
   - > id (int | unique)
   - > name (string)
   - > code (string)
   - > address (text | optional)
   - > phone (string | optional)
   - > created_at (date time)
   - > updated_at (date time)
- > Team
   - > id (int | unique)
   - > name (string)
   - > created_at (date time)
   - > updated_at (date time)
- > Estimate
   - > id (int | unique)
   - > author_id (int)
   - > contact_id (int | optional)
   - > company_id (int)
   - > notes (text | optional)
   - > total (float, 2)
   - > created_at (date time)
   - > updated_at (date time)
- > EstimateEntry
   - > id (int | unique)
   - > estimate_id (int)
   - > item
   - > description (text | optional)
   - > quantity (float,2 | default 0)
   - > unit_price (float,2 | default 0)
   - > amount (float, 2)
   - > created_at (date time)
   - > updated_at (date time)
- > Invoice
   - > id (int | unique)
   - > author_id (int)
   - > contact_id (int)
   - > number (string)
   - > title (string | optional)
   - > description (text | optional)
   - > created_at (date time)
   - > updated_at (date time)
- > InvoiceEntry
   - > id (int | unique)
   - > invoice_id (int)
   - > item
   - > description (text | optional)
   - > quantity (float,2 | default 0)
   - > unit_price (float,2 | default 0)
   - > amount (float, 2)
   - > created_at (date time)
   - > updated_at (date time)

   Here are a few important things to note about our attributes lists.

   1. All of our lists start off with an `id` attribute. This is a standard attribute which we will use to identify a specific record in the database. When we setup our migration files Laravel will automatically include this attribute in the migration files.
   2. All of the lists also contain two other columns `created_at` and `updated_at` these too, are included in our migration files by default by Laravel. These are date time types. They will store the date and time when a record is created and updated respectively. Laravel will also manage these values in our database every time we create or update a record.
   3. Some of our attributes refer to ids from other columns. For example `estimate_id` can be found in the `EstimateEntry`attribute list. The naming convention is to refer to singular case, underscore then the id. By doing so Laravel will automatically look for that column and know it is referring to a record in the `estimates` database table with the id specified. This will be put into use later when we setup the relationships between, in this case, `Estimate` and `EstimateEntry` in our models.  Anytime you see an attribute that has an _id as its name, we will refer to the relationship as `belongs to`, as in `EstimateEntry` `belongs to` `Estimate`. This is an example of a `one-to-many` relationship (one `Estimate` to many `EstimateEntry`). We will cover the code used to establish this relationship, but here's a sneak peek in case you're interested.

   > // app/Models/EstimateEntry

   > public function estimate()
{
    return $this->belongsTo(Estimate::class);
}

   We’re only describing `one` side of the `one-to-many` part of the relationship so we use the method named `belongsTo`. The other side looks like this.

   > // app/Models/Estimate

   > public function estimateEntries()
{
    return $this->hasMany(EstimateEntry::class);
}

   This is the `many` part of the `one-to-many` relationship, it uses the `hasMany` method name.

   In both cases we name the relationship method the name of the Model (singular it’s the one part of the `one-to-many` and plural if it’s the `many` part of the `one-to-many`. We then return a reference to the other’s Model class.

   We don’t have an example of a  `one-to-one` relationship, but we do have a `many-to-many` relationship with `User` and `Project`. Each of those models each say they can have many or each other.  So we would setup the relationship like this:

   > // app/Models/User

   > public function projects()
{
    return $this->belongsToMany(Project::class);
}

   > // app/Models/Project

   > public function users()
{
    return $this->belongsToMany(User::class);
}

   We will learn even more about relationships when we start building our application so don't worry if these concepts don't stick quite yet.

