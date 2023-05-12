# Git Usage

## Merging and Rebasing

We merge our branches in the following way:

* `squash+merge` when merging features to development branch using github PR interface
* rebase when merging development branch to production branch
* rebase when applying changes from development/ production to your feature branch
* rebase if merging features to development or production branch without PR (by all means should be avoided)

Our `main` and `develop` branches do not allow direct pushes without PR;

Repository settings only using `rebase` or `squash merge`, disabling `merge` with commit option.

### Rebase branch into develop

1. Ensure that your `develop` and local branches are up to date
2. Go to the branch in need of rebasing
3. Enter `git fetch origin` (This syncs your main branch with the latest changes)
4. Enter `git rebase origin/dev`
5. Fix merge conflicts that arise however you see fit
6. After fixing merge conflicts, `git add FILE` previously merge conflicted files (VScode will let you add resolved files as merge changes)
7. Enter `git rebase --continue` (or `git rebase --skip` if git complains that there were no changes after resolving all conflicts). VSCode seemingly only allows "continue" operation from GUI, skip needs to be performed via terminal.
8. Repeat steps 5 and 6 as necessary as merge conflicts arise in the subsequent commits.
9. Once the rebase is complete, enter `git push origin HEAD --force-with-lease` or use VSCode to push changes. There will be a visual indicator that changes are still to be pulled but this can be ignored.

## Branches/PRs

* Create branch
  * Branches should be created/based on the relevant environment - e.g. main branch for any hotfixes or changes that need to be directly deployed to production, develop/development branch for any other changes.
  * Use chore/ or feat/ or fix/ followed by a meaningful name for the branch, then Jira story id, using kebab-case - e.g feat/update-margin-trade-form-SOV-1234
* Make changes in a branch
  * Ideally push each change with a separate commit, and a meaningful commit message.
  * Suggested to use chore: , feat: , fix: prefixes.
* Create pull request
  * PR Title should contain Jira story id at the beginning, followed by meaningful name for the PR - e.g. "SOV-1234: Update Footer component styling"
  * No React/lint warnings should be thrown when running development server.
  * No runtime errors should be thrown when running code.
  * No errors or React warnings should be visible in browser console.
  * PR should target the correct environment that matches the one your branch was created for - e.g. `main` / `develop`.
  * Copy the Jira story link from the story you were working on (if you have one) Then paste it into the PR description. The description can also include additional information as needed.
  * Tag the PR with relevant labels:
    * enhancement - for new features or changes to an existing one
    * bug - resolving a bug or defect
    * copy - changes related to text
    * dependencies - for updating or changing package dependencies
    * do-not-merge - for PRs that should only be merged after specific conditions are met. Please include details in the PR description as to why this should not be merged yet. In cases where a PR is created but is still be worked on, the PR should be created as a Draft or marked with the "WIP" label.
    * documentation - changes related to internal documentation for this repository
    * hotfix - bugfix or change that should be deployed directly to production, without going through normal build cycle
    * refactoring - PR does not contain functional changes
    * release - PR contains changes that comprise a release
    * WIP - PR is still being worked on and should not be used as a base for other branches, or merged.
* Add yourself as Assignee
* Add reviewers
  * DistributedCollective/fe
  * DistributedCollective/qa (if QA required, which should be most PRs, unless explicitly specified on the Jira story in QA Status field).
* Add Changeset:
  * run `yarn changeset` after applying changes and follow wizard to select which packages your PR changes, if changes are major/minor/patch and write short comment what was changed/added (normally similar to your PR title)

## PR Review Process

* All PRs will receive at least 2 reviewers approval before being merged
* PRs should adhere to the guidelines outlined in this document, otherwise they may be rejected
