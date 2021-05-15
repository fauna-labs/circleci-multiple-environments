This repository contains unofficial patterns, sample code, or tools to help developers build more effectively with [Fauna][fauna]. All [Fauna Labs][fauna-labs] repositories are provided “as-is” and without support. By using this repository or its contents, you agree that this repository may never be officially supported and moved to the [Fauna organization][fauna-organization].

---

# Deploying schema changes to multiple environments with CircleCI

This repository contains a simple example of deploying schema changes to multiple environments using [CircleCI][circleci] and the [Fauna Schema Migrate][fauna-schema-migrate] tool. These instructions assume you are familiar with Fauna basics, specifically creating databases and keys in the [Fauna dashboard][fauna-dashboard].

## Resources

When you apply the migrations from this repository, Fauna creates four collections in a single transaction: *trainings*, *lessons*, *quizzes*, and *attempts*. All four collections are created in each of two child databases, *staging* and *production*.

## Pre-requisites

To deploy this application you must have a Fauna account and a CircleCI account. You can [register for a free Fauna account][fauna-register] and benefit from [Fauna’s free tier][fauna-free-tier] while you learn and build. You do not need to provide payment information until you upgrade your plan.

You can sign up for a CircleCI account by [following these instructions][circleci-signup].

## Using this repository

### Fork this repository and clone your fork

1. Fork this repo to your own GitHub account by choosing the *fork* button on this page.
1. Clone your forked repo to your own machine to make changes and push those changes through a CircleCI pipeline.

### Create databases and keys in Fauna

1. Navigate to the [Fauna dashboard][fauna-dashboard] and create a new database named `circleci`.
1. From the *DB Overview* tab, create three child databases: *development*, *staging*, and *production*.
1. Create a *Server Key* for the *development* database. Copy the secret and use it to set the environment variable `FAUNA_ADMIN_KEY` on your local machine.
1. Create a *Server Key* for the *staging* and *production* databases and store them securely. You add them to environment variables in [CircleCI contexts][circleci-contexts] in the section [Create contexts in CircleCI](#create-contexts-in-circleci).

### Deploy to your development database

1. Make sure you set the `FAUNA_ADMIN_KEY` environment variable to be your development key from the previous step.
1. Install the necessary npm packages with `npm install`.
1. Run the schema migration tool with `npx fauna-schema-migrate run`.

The schema migration tool presents a menu of options. The following is a typical local development loop.

1. Choose *state* to compare the state of migrations on disk to the state of your database in Fauna.
1. Next, choose *generate* to create migrations from the resources you define. These migrations are stored in the `fauna/migrations` directory and *should be checked in to version control*.
1. Choose *apply* to apply the generated migrations to your database in Fauna.

Return to the [Fauna dashboard][fauna-dashboard], open the *development* child database, and you should see four collections!

### Create contexts in CircleCI

Now that you understand the local development workflow, setup CircleCI to automatically build the `staging` and `main` branches when changes are pushed.

1. Navigate to the [CircleCI dashboard][circleci-dashboard]. 
1. Select the *Organization Settings* tab then create two contexts named *staging* and *production*.
1. In each context, create an environment variable named `FAUNA_ADMIN_KEY` with the value of the secret from the key you created in the previous section.

### Configure your CircleCI project

1. Close the *Organization Settings* dialog to return to the CircleCI dashboard. Select the *Projects* tab and choose *Set Up Project* on the item `circleci-multiple-environments` to configure your forked repository.
1. Choose "Skip this step" on the dialog that appears, then choose *Use Existing Config* and *Start Building*.

CircleCI starts building the default branch, `main`, using the server key from your *production* context. When the build completes, return to the [Fauna dashboard][fauna-dashboard], open the *production* child database, and you should see four collections!

### Experiment

At this point, you have not yet deployed any schema migrations to staging. Start by checking out the `staging` branch and pushing with no changes so that your staging and production environments are in sync.

```bash
git checkout -b staging && git push --set-upstream
```

What you do next is up to you! You can create, modify, and delete collections, functions, indexes, keys, roles, and access providers. Each change you make is deployed to the appropriate environment based on the branch you push.

To add additional branches and options, modify the [CircleCI config file]().circleci/config.yml).

## Cleaning up resources

Once you complete this tutorial, you may wish to remove the resources you create to avoid unexpected charges.

Navigate to the [Fauna dashboard][fauna-dashboard], select the *circleci* database, choose *Settings*, and choose *Delete*. On the confirmation screen, choose *Delete* to confirm you wish to delete your database. This deletes all associated child databases, collections, documents, keys, and indexes.

---

Copyright Fauna, Inc. or its affiliates. All rights reserved. SPDX-License-Identifier: MIT-0

[circleci]: https://circleci.com
[circleci-contexts]: https://circleci.com/docs/2.0/contexts/
[circleci-dashboard]: https://app.circleci.com
[circleci-signup]: https://circleci.com/signup/
[fauna]: https://fauna.com
[fauna-dashboard]: https://dashboard.fauna.com
[fauna-free-tier]: https://fauna.com/pricing
[fauna-labs]: https://github.com/fauna-labs
[fauna-organization]: https://github.com/fauna
[fauna-register]: https://dashboard.fauna.com/accounts/register
[fauna-schema-migrate]: https://github.com/