# Git Style Guide

This is a Git Style Guide inspired by [*How to Get Your Change Into the Linux
Kernel*](https://kernel.org/doc/html/latest/process/submitting-patches.html),
the [git man pages](http://git-scm.com/doc) and various practices popular
among the community.

# Table of contents

1. [Branches](#branches)
2. [Commits](#commits)
  1. [Messages](#messages)
3. [Merging](#merging)
3. [GitHub](#github)
4. [Misc.](#misc)

## Branches

* Choose *short* and *descriptive* names:
* Namespace your branches with your username

  ```shell
  # good
  $ git checkout -b rauhryan/oauth-migration

  # bad - too vague, no namespace, uses underscores instead of hyphens
  $ git checkout -b login_fix

  # bad - too vague
  $ git checkout -b dan/css-fixes

  # bad - no namespace
  $ git checkout -b oauth-migration
  ```

* Use *hyphens* to separate words.

* When several people are working on the *same* feature, use a *team-wide* feature branch.
  Use the following naming convention:

  ```shell
  $ git checkout -b pairing/oauth-migration # team-wide branch
  ```

  Merge at will to the team-wide branch (see ["Merging"](#merging)).
  If you hit a can't fast-forward error, you should rebase your *local"* changes and push.
  Team branches should *always* fast-forward merge.

* Delete your branch from the upstream repository after it's merged, unless
  there is a specific reason not to.

  Tip: Use the following command while being on "master", to list merged
  branches:

  ```shell
  $ git branch --merged | grep -v "\*"
  ```

  See ["Closing GitHub PR's"](#github)

## Commits 

* Each commit should be a single *logical change*. Don't make several
  *logical changes* in one commit. For example, if a patch fixes a bug and
  optimizes the performance of a feature, split it into two separate commits.

  *Tip: Use `git add -p` to interactively stage specific portions of the
  modified files.*

* Don't split a single *logical change* into several commits. For example,
  the implementation of a feature and the corresponding tests should be in the
  same commit.

* Commit *early* and *often*. Small, self-contained commits are easier to
  understand and revert when something goes wrong.

* Commits should be ordered *logically*. For example, if *commit X* depends
  on changes done in *commit Y*, then *commit Y* should come before *commit X*.

Note: While working alone on a local branch that *has not yet been pushed*, it's
fine to use commits as temporary snapshots of your work. However, it still
holds true that you should apply all of the above *before* pushing it.

Note: The above is a general rule of thumb and a philosophical goal of good 
commit and good commit messages, we are all human, but in general try to be
kind to the next developer who is `#Gblame what is this?` 6 months from now?

### Messages

* Use the editor, not the terminal, when writing a commit message:

  ```shell
  # good
  $ git commit

  # bad
  $ git commit -m "Quick fix"
  ```

  Committing from the terminal encourages a mindset of having to fit everything
  in a single line which usually results in non-informative, ambiguous commit
  messages.

* The summary line (ie. the first line of the message) should be
  *descriptive* yet *succinct*. Ideally, it should be no longer than
  *72 characters*. It should be capitalized and written in imperative present
  tense. It should not end with a period since it is effectively the commit
  *title*:

  ```shell
  # good - imperative present tense, capitalized, fewer than 50 characters
  Mark huge records as obsolete when clearing hinting faults

  # bad
  fixed ActiveModel::Errors deprecation messages failing when AR was used outside of Rails.
  ```

* After that should come a blank line followed by a more thorough
  description. It should be wrapped to *72 characters* and explain *why*
  the change is needed, *how* it addresses the issue and what *side-effects*
  it might have.

  It should also provide any pointers to related resources (eg. link to the
  corresponding issue in a bug tracker):

  ```text
  Short (72 chars or fewer) summary of changes

  More detailed explanatory text, if necessary. Wrap it to
  72 characters. In some contexts, the first
  line is treated as the subject of an email and the rest of
  the text as the body.  The blank line separating the
  summary from the body is critical (unless you omit the body
  entirely); tools like rebase can get confused if you run
  the two together.

  Further paragraphs come after blank lines.

  - Bullet points are okay, too

  - Use a hyphen or an asterisk for the bullet,
    followed by a single space, with blank lines in
    between

  The pointers to your related resources can serve as a footer 
  for your commit message. 
  
  Here is an example that is referencing issues in HelpScout:
  
  Context: https://secure.helpscout.net/conversation/564632389/11560/?folderId=478104v

  Here is an example that is referencing issues in Sentry.io:
  
  Context: https://sentry.io/omc/bonsai/issues/532169690/
  ```

  Ultimately, when writing a commit message, think about what you would need
  to know if you run across the commit in a year from now.

* If a commit is going to be squashed to another commit use the `--squash` and
  `--fixup` flags respectively, in order to make the intention clear:

  ```shell
  $ git commit --squash f387cab2
  ```

  *(Tip: Use the `--autosquash` flag when rebasing. The marked commits will be
  squashed automatically.)*

## Merging

* **Do not rewrite published history.** The repository's history is valuable in
  its own right and it is very important to be able to tell *what actually
  happened*. Altering published history is a common source of problems for
  anyone working on the project.

* However, there are cases where rewriting history is legitimate. These are
  when:

  * You are the only one working on the branch and it is not being reviewed.

  * You want to tidy up your branch (eg. squash commits) and/or rebase it onto
    the "master" in order to merge it later.

  That said, *never rewrite the history of the "master" branch* or any other
  special branches (ie. used by production or CI servers).

* Keep the history *clean* and *simple*. *Just before you merge* your branch:

    1. Make sure it conforms to the style guide and perform any needed actions
       if it doesn't (squash/reorder commits, reword messages etc.)

    2. Merging your code into master should be done using the GitHub Merge Button™

* When dealing with *long lived branches* that need to be brought "up-to-date" with
recent merges to the master branch. Prefer merging over rebasing if you have already
pushed your branch to a remote or you have an open GitHub pull-request for your branch.

   ```
   # good - uses merge
   $ git merge origin/master && git push

   # bad - uses rebase and requires a force push
   $ git rebase origin/master && git push -f

   ```

   In general this is to avoid force pushes onto code that has already been pushed to the
   remote, if you've pushed something, we should be mindful that someone else on the team
   may have pulled that code down and is in the process of collaborating with you. Collisions
   are really hard to wrap your mind around.

   As a best practice, it's"OK" to fetch changes from GitHub and merge the lastest master branch
   into your feature branch multiple times during the life cycle of your feature development.

   ```
   $ git fetch --all
  
   # notice that origin/master has changed

   $ git merge origin/master
   ```

   The earlier you catch merge conflicts the easier they are to resolve

## GitHub

### Pull Requests

* Titles should be *descriptive* yet *succinct*. Ideally, it should be no longer
  than *72 characters*. It should be capitalized and written in imperative present
  tense.

   ```
   # good -
   "Moves cluster usage polling into a background job"

   # bad - too vague
   "CSS updates"

   # bad - past tense, too vague
   "Fixed some broken tests"

   ```

* Pull request bodies should contain a brief explaination of *why*
  the change is needed, *how* it addresses the issue and what *side-effects*
  it might have.

  It should also represent any dependencies with other GitHub issues or pull requests
  using the GitHub Flavored Markdown syntax used to link issues together in GitHub:

  It should also provide any pointers to related resources (eg. link to the
  corresponding issues in GitHub or HelpScout):

  ```text
  Needs user/repo#123 #=> dependent on merging referenced PR
  Closes #456 when merged #=> automatically closes issue when
  merged and deployed
  Related to user/repo#123 #=> Related to an other body of work

  More detailed explanatory text, if necessary. Wrap it to
  72 characters. In some contexts, the first
  line is treated as the subject of an email and the rest of
  the text as the body.  The blank line separating the
  summary from the body is critical (unless you omit the body
  entirely); tools like rebase can get confused if you run
  the two together.

  Further paragraphs come after blank lines.

  - Bullet points are okay, too

  - Use a hyphen or an asterisk for the bullet,
    followed by a single space, with blank lines in
    between

  The pointers to your related resources can serve as a footer
  for your commit message.

  Here is an example that is referencing issues in HelpScout:

  Context: https://secure.helpscout.net/conversation/564632389/11560/?folderId=478104v

  Here is an example that is referencing issues in Sentry.io:

  Context: https://sentry.io/omc/bonsai/issues/532169690/
  ```

* It is helpful to give visual context when working on UI related tasks. When
  possible it is nice to include before and after screenshots or the changes
  the PR is applying.

  When including screenshots, prefer comments over embedding them into the body field.

  ```text
  ## before
  ![Alt text](http://via.placeholder.com/350x150?text=before)
  ## after
  ![Alt text](http://via.placeholder.com/350x150?text=after)
  ```

* Closing pull requests

In general, only merge a pull request when all the automated checks are finished and successful. Unless its
an emergency or otherwise nessasary, avoid merging pull requests without *at least one* approved code review
from another teammate.

Prefer using the GitHub UI and the Merge button when merging code into the default branch (master) over using
merging your code manually with git. If you don't want to do that or consider yourself an expert terminal cow
poke, merge using the `--no-ff` flag and include the pull request number in your commit message

  ```
  $ git merge rauhryan/weekly-performance-email --no-ff -m "Merging pull request #456"
  ```

This keeps context in the master branch with "merge bumps" that have nice url's that reference back to
the pull request that merged in the feature/bug fix

When working on long lived applications, context and git history is paramount to long term maintainability.
This is nuanced and there are *grey-areas* and holy wars over merge vs rebase which are valid in contexts
of maintaining open-source libraries where backward compatibility is important and semver is required.
If there is no *good* reason to rewrite git history, closing pull requests with the "Create merge commit" option
should *always* be prefered over *Squash and merge* and *Rebase and merge*

In summary:

  * Don't merge without someone else's eyeballs
  * Wait till all the statuses are successfull
  * Use the MergeButton with Create a merge commit

### GitHub Issues

[How to write a good bug report](http://testthewebforward.org/docs/bugs.html)

A good GitHub issues should container the following:

* Summary: The goal of summary is to make the report searchable and uniquely identifiable.

  ```text
  # bad
  Drag crash

  # good
  Drag-selecting the table element on the clusters page crashes Chrome 49 on OSX 10.7
  ```

* A brief description of the issue with any related context that is useful

* Steps to reproduce the problem

  ```text
  1. Load the attached testcase in Browser XYZ

  2. Scroll to the bottom of the page

  3. Click the link

  4. Press tab to navigate links
  ```

* Expected results

  Brief explaination of what the expected outcome of your steps to reproduce should have
  been

* Actual results

  Brief explaination of what actually happens.

  :+1: to include screenshots, stacktraces, error messages, or links
  to Sentry errors or HelpScout tickets

## Misc.

* There are various workflows and each one has its strengths and weaknesses.
  Whether a workflow fits your case, depends on the team, the project and your
  development procedures.

  That said, it is important to actually *choose* a workflow and stick with it.

* *Be consistent.* This is related to the workflow but also expands to things
  like commit messages, branch names and tags. Having a consistent style
  throughout the repository makes it easy to understand what is going on by
  looking at the log, a commit message etc.

* *Test before you push.* Do not push half-done work.

* Use [annotated tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Annotated-Tags)
  for marking releases or other important points in the history. Prefer
  [lightweight tags](http://git-scm.com/book/en/v2/Git-Basics-Tagging#Lightweight-Tags)
  for personal use, such as to bookmark commits for future reference.

* Keep your repositories at a good shape by performing maintenance tasks
  occasionally:

  * [`git-gc(1)`](http://git-scm.com/docs/git-gc)
  * [`git-prune(1)`](http://git-scm.com/docs/git-prune)
  * [`git-fsck(1)`](http://git-scm.com/docs/git-fsck)

# License

![cc license](http://i.creativecommons.org/l/by/4.0/88x31.png)

This work is licensed under a [Creative Commons Attribution 4.0
International license](https://creativecommons.org/licenses/by/4.0/).

# Credits

Agis Anastasopoulos / [@agisanast](https://twitter.com/agisanast) / http://agis.io
... and [contributors](https://github.com/agis-/git-style-guide/graphs/contributors)!
