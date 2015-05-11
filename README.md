# encore-ui-screenshots

A place to catalog the visual history of https://github.com/rackerlabs/encore-ui through visual regression testing in Selenium.

## How it's set up

In order to track visual changes in the framework using git, there has to be a place where these images live. If it's placed in the greater EncoreUI project, every contributor will need to clone megabytes of unimportant archives containing large binary files. You could use a submodule to avoid the penalty of cloning it, but you can still find yourself in trouble if a contributor notices a submodule listed in the project, and updates it out of curiosity. This clone could take a while on a slow connection.

## Leveraging Submodules

This system uses a submodule under the hood, but the submodule is *created* in the pull request validation automation. To a developer, the screenshots repository is invisible.

In Travis CI, once a build has passed, the submodule is cloned, `cd`'d into, and a branch is checked out. The name of the branch is always `SHA-{{COMMIT_SHA_FROM_LAST_COMMIT_IN_ENCORE_PULL_REQUEST}}`. The screenshots are added, pushed, and a script takes that branch and opens a pull request that mentions the original EncoreUI pull request. This creates the effect of a "notification" in the pull request that there is a screenshot diff available for that changeset.

## Keeping the Submodule Small

If a pull request is triggered, and a visual regression test runs, there will be a pull request that is filed against that changeset. Should there be an error in the original EncoreUI pull request, there may be some new changes pushed on top of the pull request during manual code review. This *will trigger a new visual regression run*, complete with a new screenshot pull request that is again, linked in the original EncoreUI pull request.

Imagine what would happen if 40+ commits are built against EncoreUI pull requests in a week. If there are a few images in each, there's be hundreds of images more or less duplicated across multiple commits. All of this information will need to be cloned into the submodule on every Travis CI pull request build! This can easily grow to megabytes of data that will add time to each pull request validation as time passes.

The solution is using a *fork* of the encore-ui-screenshots repository! The automation account pushes up the many, many unused branches to *the fork*, and only the accepted pull requests that make it back into master will be added into the upstream repository. This upstream repository is what gets cloned on every build, and it remains as small as it can be, keeping things optimally fast.

## Cleaning Up

You're probably going to get a few angry emails sent to the automation account's fork, eventually. As of May 2015, Github states that the largest repository size allowed is 1GB, which conservatively means about 25,000 screenshots (plus a couple thousand, likely). To avoid that situation, periodically delete all branches that are stale, and [push those delete branch operations to github as well](http://stackoverflow.com/questions/2003505/delete-a-git-branch-both-locally-and-remotely).
