# S2-06: Naming Conventions

One of the single most important things I see new developers do is ignore naming conventions. In code review I’ve seen it all; function names misspelled, too long, too short, misleading, mixture of capitalization styles, etc. It’s all a bit messy out there, so I thought it would be a good idea to cover the naming conventions we will be using in this workshop. Furthermore, I’ll explain why things are the way they are, as we go.

Instead of just throwing you everything all at once, I’m breaking these conventions into the context in which they will be used. You’ll find several competing strategies here, but when considered in context, they will correct and serve a purpose.

### Brush up on your lingo..

Before we get to the content of a name, I want you to know what I mean when I refer to the specific capitalization styles.

1. Flat Case Has no separation between words and all words are lowercase

```javascript
mypackagename
```

1. Camel Case (also called Upper Camel Case or Capitol Camel Case):  Has no spaces between words and all words are uppercase.

```php
MyClassName() { ... }
```

2. Lower Camel Case The same as Camel Case, however the first word is lowercase.

```php
myClassName() { ... }
```

1. Snake Case Spaces between words is replaced with an underscore `_` and all the words are lowercase.

```sql
my_database_table_name
```

1. Kebab Case Spaces between words is replaced with a hyphen - and all words are lowercase.

```css
.my-css-class-name { ... }
```

   There are more than the ones listed above, and some do go by other names as well, but this is how I will be referring to them in this workshop.

   Here’s a set of tables of every naming convention we will be using in our Laravel project broken up by their categories.

   ### Files in Laravel

| **Files**   | **Case**   | **Term** | **Example**              |
| ----------- | ---------- | -------- | ------------------------ |
| Model       | Camel Case | Singular | class `User.php`         |
| Controllers | Camel Case | Singular | `UserController.php`     |
| Classes     | Camel Case | Both     | `MyClassName.php`        |
| Migrations  | Snake Case | Both     | `create_users_table.php` |
|             |            |          |                          |
| Providers   | Camel Case |          | AppServiceProvider.php   |
| Config      | Kebab Case | Both     | `my-config.php`          |
| Observers   | Camel Case |          | `UserObserver.php`       |
| Factories   | Camel Case | Singular |                          |
|             |            |          |                          |

   ### Coding Conventions

| **Names**     | **Case**         | **Term** | **Example**             |
| ------------- | ---------------- | -------- | ----------------------- |
| Classes       | Camel Case       | Singular | `class FieldType()`     |
| Controllers   | Camel Case       | Singular | `FieldTypeController()` |
| Constants     | Upper Snake      | Both     | `MY_CONSTANT`           |
| Functions     | Lower Camel Case | Both     | `updateRecords()`       |
| Variables     | Lower Camel Case | Both     | `$myVarName`            |
| Env Vars      | Upper Snake      | Both     | `DATABASE_NAME`         |
| JSON Keys     | Kebab Case       | Both     | `“my-key-name”: { }`    |
| Test Methods  | Snake Case       | Both     | `test_my_test_name()`   |
| Routes / URLs | Kebab Case       | Plural   | `/field-types`          |
| DB Tables     | Snake Case       | Plural   | `field_types`           |
| DB Fields     | Snake Case       | Singular | `field_type_id`         |

   ### Version Control Commit Messages

   A well-written commit message is invaluable. It not only helps you remember what changes you made in the commit, perhaps more importantly, it tells the other developers what they should expect to see when they merge your changes into theirs.

   As mentioned before, the commit message is required in order for you to do a commit. If you try to commit without one, you’ll be forced into a text editor to provide your message there before the commit will be accepted.

   The convention I follow is I try to include an action word like “updates, fixes, adds, removes” as my first word, then I provide a brief description of the change I made.

   Here are a few examples of this convention in practice.

```Bash
$ git commit -m "Updates UserController to include new method getUserProfile()"
```

```Bash
$ git commit -m "Updates name of myMethod() to more descriptive name verifyUserEmailAddress() in UserController"
```

```Bash
$ git commit -m "Fixes typo in miMethod() to myMethod()"
```

```Bash
$ git commit -m "Removes unused method named myMethod() from UserController"
```

   In each of these commit messages it’s pretty clear what I’m doing. Also notice how the names of the controllers and methods I mentioned in my commit messages helps us understand what the changes are because they too are well named?

   The goal is to make your commit methods clear and succinct. When your changes impact multiple files, there’s no need to mention them all. The changed files and the details of all your changes will be included in your commit. In those cases, a summary of what you did is all that is needed.

```Bash
$ git commit -m "Fixed a bug that prevented a user from being assigned a role when registerring for an account"
```

That commit message tells us all we need to know. What was the bug, it more important than why it was a bug. Anyone interested in the “why” can review your commit, see the changed files and every change you made to fix the bug.

