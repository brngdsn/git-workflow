# git workflow

Using `git` effectively comes with practice. In this readme, we'll discuss using Vincent Driessen's model for branching, and an overview of semantic versioning. You can see the model [here](https://nvie.com/img/git-model@2x.png).

# branches

In general, there is a `master` branch, a `develop` branch, and `hotfix` branches. The `master` branch will always be "stable", and reflect what's in production (quoting stable since there's always possibility there's a bug requiring a hot fix, but it's not unstable like the `develop` branch). The `develop` branch is essentially an unstable integration branch where developers will be submitting PRs to merge their work into before merging `develop` into `master`, and tagging master with the version.

# semantic versioning

You may read the standard [here](https://semver.org/). In general, semantic versioning gives insight into what the current release means in relation to the history of releases. It uses an `x.y.z` iteration of `major.minor.patch` format. In general, the release manager or team lead will only ever increment a version, and the version is determined at the very last minute right before releasing.

In a nutshell, any backwards-incompatible changes would justify a `major` increment, like going from `3.11.8` to `4.0.0`. Any backwards-compatible changes--which is the most common iteration--will increase the `minor` increment, like going from `3.11.8` to `3.12.0`. Any bug fixes or hot fixes that are backwards-compatible iterate the `patch` increment, like going from `3.11.8` to `3.11.9`.

# feature branches

Developers will typically begin with a user story. In such a case, the following outlines a starting point for any new feature:

```
$ git checkout develop
$ git pull --rebase upstream develop
$ git checkout -b feature/idi-1234-eg-summary
```

What we did here is tell git to switch to the our local development branch, pull the most recent updates from remote, and then checkout a new feature branch from there (following a specific pattern, `feature/<jira-ticket>-<short-summary>`).

Now we can start editting files. Our goals are to commit early, and commit often. Commits should typically be dependency free (i.e., if we edit more than one file, we should determine which file should commit first, instead of commiting both files at once). We can use some of the git commands to figure out how to best makes out commits. First, we'll check the status of our repository:

```
$ git status
```

Which will show all the files that have been changed (this includes new files and deleted files). Second, we can check each file individually for what changes occured:

```
$ git diff src/eg-file.js
```

Which will show a detailed view of what lines we deleted, and what lines we added. Finally, we can add each change and commit it with a meaningful message that will also include the ticket number you're working on:

```
$ git add src/eg-file.js && git commit -m "changed egMethod() signature"
```

Which will both stage your changes for commit, and then actually make the commit with a message. Once we're done adding and committing all our changes, we check our history, and then push it to remote upstream:

```
$ git log
$ git push upstream feature/idi-1234-eg-summary
```

# pull requests

Once we've pushed our feature branch to remote upstream, we can submit a pull request to merge our changes into the development branch. The development branch is an unstable integration branch that will always be either at par or ahead of master. To submit a PR, go to the repository on github, and next to the branches drop down button, click on `New pull request`. The next page will by default select the `master` branch on the left, but developers will typically only be merging to `develop`. Chose your feature branch from the left drop down options. Github will check if the branches can be merged automically, and tell you on the spot. Once you've chosen your branches, add a title to the PR. Then, write a short description of what you did, including the jira ticket. If your feature has UI effects, be sure to grab and attach a gif of the UX for your feature. Finally, just click the grene button at the bottom to submit the PR. This will then kick off a couple of things, typically any CI, CD, and code reviews. Github integrates nicely with CI/CD services, and if connect to the repository, will tell you right there in the PR if they succeeded or not. Once a PR has been approved, the designated team lead or any other authorized developers click the button to merge the PR into the development branch.

# merge conflicts

Github will tell you almost instantly if there are merge conflicts. Most conflicts can be resolved now via their UI. You'll have to analyze the conflicting files and changes, and choose which lines you'll want to keep and which lines you'll toss, then simply save the file.

# hot fixes

If there's a bug in production, then the workflow is a little bit different. We'll end up pull the latest `master` branch, checking out a bug fix branch, pushing to remote, submitting a PR to `master` instead of `develop`, and finally merging the bugfix branch in the `develop` branch as well as any developer's feature branch that has already branched from develop. The reason we don't merge into `develop` first, and then into `master`, is because `develop` is always either at par or ahead of `master`. So we don't want to merge new features already in `develop` into the `master` branch.