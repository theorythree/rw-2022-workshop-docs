# S1-01: Development Workflow

Before we begin setting up your environment and getting into the coding of your project, it is important for you to have an understanding of *how* you will be working on your project.

This is the standard workflow of a developer, regardless of the type of project you may be working on, the environments, version control, and steps described below should apply.

### Environments

A developer typically works in different environments as part of their overall workflow. The following list is ordered chronologically in which the process takes place.

1. `Local` - this is the name given to the environment that is on the developer's computer only. It contains a representation of the final site (database, webpages, etc), but it exists only on the developer’s computer. This allows the developer to build and test new features and enhancements in a safe environment without impacting the other environments that may be using the site. Once the developer has had an opportunity to build and test their contribution to the project, their code changes are “merged” (more on this later) into the “test environment”.
2. `Test` - this environment is hosted on a shared server with the other developers that may be contributing to the project and the testers who will be reviewing the work for quality assurance and the discovery of potential bugs. The purpose of this environment is to allow for more people to review and test the codebase in more of a shared environment. If, for example, a bug is found, it can be fixed before the code is merged into the live site where actual customers and/or real users would be disrupted by the bug. Like the local environment, the test environment has its own copy of the codebase and database, so that testing can be conducted without impacting the live site.
3. `Staging` (optional) - sometimes the testing environment feeds into a “staging” environment because it requires another level of testing before the developers and testers would feel comfortable releasing the code to the public. The purpose of the staging environment is to test the codebase against the “live” database. This environment is useful when you have to test the new code against live (or real) data. Because this environment is still restricted to developers and testers, if offers one last opportunity to see how the live site will behave once the updated codebase is actually released.
4. `Production` (sometimes referred to as “live”) - the production environment is the final destination for the codebase and database updates. Once the code and any database schema changes have been merged in to the live site, they are considered released. Sometimes this step in the workflow is referred to as “released”, “published”, “live”, or “launched” to denote that the updates made have reached their final destination in the workflow.

