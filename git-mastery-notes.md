# Git Mastery: Interview Revision Notes

Questions from the [Git Mastery](https://gitmastery.me/) beginner game, each with the command, an explanation, and what to say in an interview.

## Contents

1. [Setup and commits](#1-setup-and-commits)
2. [Removing and renaming files](#2-removing-and-renaming-files)
3. [Branches](#3-branches)
4. [Deleting branches](#4-deleting-branches)
5. [Remotes: push and upstream](#5-remotes-push-and-upstream)
6. [Basics: status, config, help](#6-basics-status-config-help)
7. [Merging](#7-merging)
8. [Commit history: log, diff, show, blame](#8-commit-history-log-diff-show-blame)
9. [More on remotes: clone, fetch, pull](#9-more-on-remotes-clone-fetch-pull)
10. [Undoing changes: restore, reset, revert](#10-undoing-changes-restore-reset-revert)
11. [Advanced: rebase, stash, tag, cherry-pick, bisect](#11-advanced-rebase-stash-tag-cherry-pick-bisect)
12. [Quick reference](#quick-reference)
13. [Interview questions, beginner to advanced (95 Q&A)](#interview-questions-beginner-to-advanced)

---

## 1. Setup and commits

### Initiate git

```bash
git init
```

Turns the current folder into a Git repository by creating a hidden `.git/` folder. That folder stores all history and settings. Run it once per project.

### Create a commit with a message

```bash
git add .                         # stage the changes
git commit -m "Add login page"    # save a snapshot with a message
```

A commit is a saved snapshot of your staged changes.

- `git add` moves changes into the **staging area** (index).
- `git commit` records only what is staged.
- `-m` supplies the message inline. Without it, Git opens your editor.

**Interview tip:** Write messages in the imperative: "Add login page", not "Added login page".

**Shortcut:** `git commit -am "message"` stages and commits changes to files Git already tracks. It does not include new files.

---

## 2. Removing and renaming files

### Remove a file from both the working directory and the index

```bash
git rm old-file.txt
git commit -m "Remove old-file.txt"
```

`git rm` does two things at once: it deletes the file from disk and stages the deletion. If you used plain `rm` instead, you would still need `git add` to stage the deletion.

| Goal | Command |
|---|---|
| Delete from disk and from Git | `git rm file` |
| Remove from Git only, keep the file on disk | `git rm --cached file` |
| Force-remove a file with uncommitted changes | `git rm -f file` |

`git rm --cached` is useful when you committed something by mistake, such as a `.env` file, and want Git to stop tracking it.

### Rename `src/app-config.js` to `src/config.js` using `git mv`

```bash
git mv src/app-config.js src/config.js
```

`git mv` renames the file and stages the rename in one step. It is the same as:

```bash
mv src/app-config.js src/config.js
git add src/app-config.js src/config.js
```

Git doesn't store renames explicitly. It detects them by noticing that the content is nearly identical, so history is kept with `git log --follow`.

### Commit the rename with a descriptive message

```bash
git commit -m "Rename app-config.js to config.js"
```

A good message says what changed and why, e.g. `Rename app-config.js to config.js for a simpler import path`.

---

## 3. Branches

A **branch** is a movable pointer to a commit. It lets you work on something without touching the main line of development.

### Display all branches in your repository

```bash
git branch        # local branches (current one has a *)
git branch -a     # local and remote-tracking branches
git branch -r     # remote-tracking branches only
git branch -v     # include the latest commit on each branch
```

`-a` is the answer for "all branches".

### Create a new branch named `feature` and switch to it

```bash
git switch -c feature
```

`-c` means "create". The classic equivalent is `git checkout -b feature`.

If you only want to create the branch without switching:

```bash
git branch feature
```

> **Gotcha:** You can't have a branch named `feature` and another named `feature/search-filters` at the same time. Git stores branches as files under `refs/heads/`, so `feature` would have to be both a file and a folder. Pick one naming style.

### Switch between branches

```bash
git switch main
git switch feature
git switch -        # jump back to the previous branch
```

`git switch` was added in Git 2.23 to do only one job: change branches. It is clearer than `checkout`.

### Switch to another branch with the classic command

```bash
git checkout main
```

`git checkout` is the older command. It does several jobs (switching branches, restoring files, detaching HEAD), which is why `switch` and `restore` were introduced. Both still work.

| Task | Modern | Classic |
|---|---|---|
| Switch branch | `git switch main` | `git checkout main` |
| Create and switch | `git switch -c feature` | `git checkout -b feature` |
| Discard changes in a file | `git restore file` | `git checkout -- file` |

---

## 4. Deleting branches

### Delete the merged branch `feature/search-filters`

```bash
git branch -d feature/search-filters
```

`-d` is the **safe delete**. Git refuses if the branch has commits that haven't been merged, so you can't lose work by accident.

### Force-delete the abandoned branch `experiment/new-ui`

```bash
git branch -D experiment/new-ui
```

`-D` is the same as `--delete --force`. It deletes the branch even if it was never merged. Use it only when you are sure you don't need that work. The commits can still be recovered for a while through `git reflog`.

| Flag | Meaning | Use when |
|---|---|---|
| `-d` | Safe delete (merged branches only) | The work is merged |
| `-D` | Force delete | The work is abandoned |

You can't delete the branch you are currently on. Switch away first.

**Delete a remote branch:**

```bash
git push origin --delete feature/search-filters
```

---

## 5. Remotes: push and upstream

A **remote** is a copy of your repository hosted somewhere else, such as GitHub or GitLab.

### Add a remote repository

```bash
git remote add origin https://github.com/username/repo.git
git remote -v        # verify
```

- `origin` is the conventional name for your main remote. It is only a nickname for the URL.
- `git remote -v` lists your remotes with their URLs.

### Push your local commits to the remote repository

```bash
git push origin main
```

This uploads commits from your local `main` branch to the remote called `origin`.

### Understand the difference between a local commit and a remote push

| | `git commit` | `git push` |
|---|---|---|
| Where it acts | Only on your machine | Sends data to the remote |
| What it does | Saves a snapshot to local history | Uploads local commits to the remote |
| Needs internet? | No | Yes |
| Visible to teammates? | No | Yes, after the push |

Think of it like writing a document (commit) versus emailing it (push). You can commit many times offline and push later.

### Publish the `login-form` branch with upstream tracking

```bash
git push -u origin login-form
```

- `-u` is short for `--set-upstream`.
- It pushes the branch **and** links your local `login-form` to `origin/login-form`.
- After that, Git knows where this branch pushes to and pulls from.

### Commit the improved error messages

```bash
git add .
git commit -m "Improve error messages on login form"
```

### Push again without any arguments

```bash
git push
```

This works because the upstream was set earlier with `-u`. Without it, Git would say:
`fatal: The current branch login-form has no upstream branch.`

With tracking set up, `git push` and `git pull` work with no arguments, and `git status` shows whether you are ahead of or behind the remote.

---

## 6. Basics: status, config, help

### `git status`: see what's going on

```bash
git status       # full output
git status -s    # short format
```

Shows the current branch, which files are staged, which are modified but not staged, and which are untracked. Run it often, before every commit.

### `git config`: set your identity and preferences

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --list          # show all settings
```

Git stamps every commit with your name and email, so set them first.

| Scope | Flag | Stored in |
|---|---|---|
| This repo only | `--local` (default) | `.git/config` |
| Your user, all repos | `--global` | `~/.gitconfig` |
| Whole machine | `--system` | system config file |

The narrowest scope wins.

### `git help`: built-in docs

```bash
git help commit      # full manual page
git commit --help    # same thing
git commit -h        # short usage summary
```

---

## 7. Merging

### `git merge`: join two histories

```bash
git switch main          # go to the branch that should receive the changes
git merge feature        # bring feature's commits into main
```

You always merge **into the branch you're on**.

| Type | When it happens | Result |
|---|---|---|
| **Fast-forward** | `main` hasn't moved since `feature` branched | Git just moves the `main` pointer forward. No new commit |
| **Merge commit** (3-way) | Both branches have new commits | Git creates a new commit with two parents |

Force a merge commit even when a fast-forward is possible with `git merge --no-ff feature`.

### Merge conflicts

A conflict happens when both branches changed the same lines. Git stops and marks the file:

```
<<<<<<< HEAD
your branch's version
=======
the other branch's version
>>>>>>> feature
```

To resolve it:

```bash
# 1. Edit the file, keep what you want, delete the markers
git add the-file.js
git commit               # completes the merge
```

Changed your mind? `git merge --abort` returns you to the state before the merge.

After merging, you can delete the finished branch with `git branch -d feature`.

---

## 8. Commit history: log, diff, show, blame

### `git log`: view history

```bash
git log                       # full history
git log --oneline             # one line per commit
git log --oneline --graph --all   # branch graph of everything
git log -n 5                  # last 5 commits
git log --author="Name"       # filter by author
git log -p                    # include the diff for each commit
git log --follow file.js      # history of a file, across renames
```

### `git diff`: see changes

```bash
git diff                  # working directory vs staging area (unstaged changes)
git diff --staged         # staging area vs last commit (what will be committed)
git diff main..feature    # differences between two branches
git diff abc123 def456    # differences between two commits
```

Remember: plain `git diff` doesn't show staged changes. That surprises many beginners.

### `git show`: inspect one object

```bash
git show              # latest commit: message and changes
git show abc123       # a specific commit
git show v1.0         # a tag
git show HEAD:README.md   # a file as it was in a commit
```

### `git blame`: who changed each line

```bash
git blame file.js
git blame -L 10,20 file.js    # only lines 10 to 20
```

Prints the commit, author and date next to every line. Use it to find out **why** a line was written and who to ask. It isn't for assigning fault.

---

## 9. More on remotes: clone, fetch, pull

### `git clone`: copy a remote repository

```bash
git clone https://github.com/username/repo.git
git clone https://github.com/username/repo.git my-folder   # custom folder name
git clone --depth 1 <url>                                  # latest snapshot only (faster)
git clone -b dev <url>                                     # start on branch dev
```

Clone downloads the full history, creates a folder, checks out the default branch, and sets up `origin` automatically. You don't need `git init` or `git remote add`.

### `git fetch`: download, but don't change your files

```bash
git fetch              # from the default remote
git fetch origin
```

Updates your remote-tracking branches (like `origin/main`) without touching your working files or local branches. It is safe to run any time, so you can review before merging.

### `git pull`: fetch and integrate

```bash
git pull               # fetch + merge
git pull --rebase      # fetch + rebase (cleaner, linear history)
```

**`git pull` = `git fetch` + `git merge`.**

| | `fetch` | `pull` |
|---|---|---|
| Downloads new commits | Yes | Yes |
| Changes your local branch and files | No | Yes |
| Can cause conflicts | No | Yes |

### `git remote`: manage remotes

```bash
git remote -v                          # list remotes with URLs
git remote add origin <url>            # add
git remote rename origin upstream      # rename
git remote set-url origin <new-url>    # change the URL
git remote remove origin               # remove
git remote show origin                 # details about a remote
```

---

## 10. Undoing changes: restore, reset, revert

Which command to use depends on **what you want to undo** and **whether the commit is already pushed**.

### `git restore`: undo file changes

```bash
git restore file.js                  # discard unstaged changes (can't be undone)
git restore --staged file.js         # unstage a file, keep the changes
git restore --source=HEAD~1 file.js  # bring back the file as of one commit ago
```

### `git reset`: move the branch back

```bash
git reset --soft HEAD~1    # undo the commit; keep changes staged
git reset HEAD~1           # undo the commit; keep changes unstaged (--mixed, default)
git reset --hard HEAD~1    # undo the commit and DELETE the changes
```

| Mode | HEAD / branch | Staging area | Working directory |
|---|---|---|---|
| `--soft` | moves | kept | kept |
| `--mixed` (default) | moves | reset | kept |
| `--hard` | moves | reset | reset (changes lost) |

`reset` **rewrites history**. Never use it on commits you have already pushed and others may have pulled. A mistaken `--hard` can sometimes be recovered with `git reflog`.

### `git revert`: undo by adding a new commit

```bash
git revert abc123      # new commit that reverses abc123
git revert HEAD        # reverse the latest commit
```

`revert` doesn't delete history. It adds a new commit that applies the opposite changes, so it is **safe for shared or pushed branches**.

| | `reset` | `revert` |
|---|---|---|
| Changes history? | Yes, rewrites it | No, adds a commit |
| Safe on pushed commits? | No | Yes |
| Use for | Local clean-up | Undoing something already shared |

---

## 11. Advanced: rebase, stash, tag, cherry-pick, bisect

### `git rebase`: replay commits on a new base

```bash
git switch feature
git rebase main            # move feature's commits on top of the latest main
```

Rebase copies your commits and re-applies them on top of another branch, giving a **straight-line history** instead of a merge commit. The commits get new hashes.

Interactive rebase cleans up your own commits:

```bash
git rebase -i HEAD~3       # reorder, squash, reword or drop the last 3 commits
```

If there is a conflict: fix it, `git add` the file, then `git rebase --continue`. To cancel: `git rebase --abort`.

> **Golden rule:** never rebase commits that are already pushed and shared. It rewrites history for everyone else.

| | `merge` | `rebase` |
|---|---|---|
| History | Keeps the branching as it happened | Linear and tidy |
| Rewrites commits? | No | Yes (new hashes) |
| Safe on shared branches? | Yes | No |

### `git stash`: shelve work temporarily

```bash
git stash                      # save uncommitted changes, clean the working directory
git stash push -m "wip login"  # with a label
git stash -u                   # include untracked files
git stash list                 # see all stashes
git stash pop                  # re-apply the latest stash and remove it
git stash apply                # re-apply but keep it in the list
git stash drop                 # delete the latest stash
```

Use it when you need to switch branches but aren't ready to commit, for example when an urgent bug fix comes in.

### `git tag`: mark a point in history

```bash
git tag                          # list tags
git tag v1.0                     # lightweight tag (just a label)
git tag -a v1.0 -m "Release 1.0" # annotated tag (stores author, date, message)
git tag -d v1.0                  # delete locally
git push origin v1.0             # push one tag
git push origin --tags           # push all tags
git push origin --delete v1.0    # delete on the remote
```

Tags are normally used for releases. They are not pushed automatically with `git push`. Prefer **annotated** tags for releases.

### `git cherry-pick`: copy a specific commit

```bash
git switch main
git cherry-pick abc123            # apply that one commit here
git cherry-pick abc123 def456     # several commits
```

Applies the changes from a commit on another branch as a **new commit** here. It is useful for pulling a hotfix into a release branch. If there is a conflict: resolve it, `git add`, then `git cherry-pick --continue` (or `--abort`).

### `git bisect`: binary search for the commit that broke things

```bash
git bisect start
git bisect bad                 # the current commit is broken
git bisect good v1.0           # this older commit was fine
# Git checks out a commit in the middle. Test it, then run:
git bisect good                # or: git bisect bad
# ...repeat until Git names the first bad commit
git bisect reset               # return to where you started
```

Automate it with `git bisect run ./test.sh`. With 1,000 commits, bisect needs only about 10 steps.

---

## Quick reference

| Task | Command |
|---|---|
| Start a repository | `git init` |
| Stage changes | `git add .` |
| Commit | `git commit -m "message"` |
| Remove file (disk and index) | `git rm file` |
| Rename file | `git mv old new` |
| List branches (all) | `git branch -a` |
| Create and switch branch | `git switch -c name` / `git checkout -b name` |
| Switch branch | `git switch name` / `git checkout name` |
| Delete merged branch | `git branch -d name` |
| Force-delete branch | `git branch -D name` |
| Add remote | `git remote add origin <url>` |
| Push | `git push origin main` |
| Push and set upstream | `git push -u origin name` |
| Push (upstream already set) | `git push` |
| Check status | `git status` |
| Set name / email | `git config --global user.name "Name"` |
| Get help | `git help <command>` |
| Merge a branch into the current one | `git merge name` |
| View history | `git log --oneline --graph` |
| Unstaged / staged changes | `git diff` / `git diff --staged` |
| Show one commit | `git show <hash>` |
| Who changed each line | `git blame file` |
| Copy a remote repo | `git clone <url>` |
| Download without merging | `git fetch` |
| Download and merge | `git pull` |
| Discard file changes | `git restore file` |
| Unstage a file | `git restore --staged file` |
| Undo last commit, keep changes | `git reset --soft HEAD~1` |
| Undo a commit safely (pushed) | `git revert <hash>` |
| Replay commits on another branch | `git rebase main` |
| Shelve and restore work | `git stash` / `git stash pop` |
| Create an annotated tag | `git tag -a v1.0 -m "msg"` |
| Copy one commit | `git cherry-pick <hash>` |
| Find the commit that broke it | `git bisect start` |
| Fix the last commit | `git commit --amend` |
| Recover lost commits | `git reflog` |
| Delete untracked files (dry run first) | `git clean -n` then `git clean -fd` |
| Safer force push | `git push --force-with-lease` |
| Revert a merge commit | `git revert -m 1 <hash>` |
| Search history for a string | `git log -S "text"` |

---

## Interview questions, beginner to advanced

**How to use this section:** 95 numbered questions in five levels. For each one, practise saying the answer out loud in three parts: **definition, command, gotcha.** Interviewers reward the gotcha most.

| Level | Questions | Aimed at |
|---|---|---|
| [Level 1: Beginner](#level-1-beginner-q1-to-q20) | Q1 to Q20 | Freshers, any role |
| [Level 2: Intermediate](#level-2-intermediate-q21-to-q45) | Q21 to Q45 | 0 to 2 years |
| [Level 3: Advanced](#level-3-advanced-q46-to-q66) | Q46 to Q66 | Product companies, 2+ years |
| [Level 4: Real-world scenarios](#level-4-real-world-scenarios-q67-to-q83) | Q67 to Q83 | "What would you do if..." rounds |
| [Level 5: Workflow and team practices](#level-5-workflow-and-team-practices-q84-to-q95) | Q84 to Q95 | Senior, system and process rounds |

---

## Level 1: Beginner (Q1 to Q20)

### Q1. What is Git?
Git is a **distributed version control system**. It records snapshots of your project over time so you can see history, undo mistakes, work on features in parallel (branches) and collaborate with others. It was created by Linus Torvalds in 2005 for the Linux kernel.

### Q2. What is the difference between Git and GitHub?
Git is the **tool** that runs on your machine and tracks changes. GitHub is a **hosting service** for Git repositories that adds pull requests, issues, code review, permissions and CI. GitLab and Bitbucket are alternatives. You can use Git with no GitHub at all.

### Q3. What is a repository?
A repository (repo) is a project folder together with its full history, stored in the hidden `.git` directory. A **local** repo is on your machine. A **remote** repo is hosted elsewhere, such as GitHub.

### Q4. Centralized vs distributed version control: what is the difference?
In a centralized system like SVN, there is one central server holding the history, and you need it to commit. In a distributed system like Git, **every clone has the full history**, so you can commit, branch and view logs offline, and there is no single point of failure.

### Q5. What are the three areas (and states) in Git?
- **Working directory:** your files on disk (state: *modified*).
- **Staging area / index:** changes selected for the next commit (state: *staged*).
- **Repository:** the committed history (state: *committed*).

Flow: edit, then `git add`, then `git commit`.

### Q6. What does `git init` do?
It creates a new repository by making the hidden `.git` folder in the current directory. Run it once per project. If the project already exists on a server, use `git clone` instead.

### Q7. What is the difference between `git add` and `git commit`?
`git add` copies changes into the staging area. `git commit` records what is staged as a permanent snapshot in the history. Changes you haven't added are not included in the commit.

### Q8. What is the difference between `git add .`, `git add -A` and `git add -u`?
- `git add .` stages new, modified and deleted files in the current directory and below.
- `git add -A` does the same for the whole repository.
- `git add -u` stages only files Git already tracks (modified and deleted). It does **not** stage new files.

### Q9. What is a commit?
A commit is a snapshot of the project with a unique SHA hash, an author, a timestamp, a message and a pointer to its parent commit(s). The chain of parents forms the history.

### Q10. Why does the staging area exist?
It lets you build clean, focused commits. You can edit many files but stage and commit only the related changes. `git add -p` even lets you stage part of a file.

### Q11. What does `git status` show?
The current branch, whether you are ahead of or behind the remote, staged changes, unstaged changes and untracked files. Run it before every commit.

### Q12. What are tracked, untracked and ignored files?
- **Tracked:** Git knows the file (it was committed or staged).
- **Untracked:** new files Git hasn't been told about.
- **Ignored:** files matching a pattern in `.gitignore` (e.g. `node_modules/`, `.env`). Git skips them.

### Q13. What is a branch?
A branch is a lightweight, movable pointer to a commit. Creating one is nearly free (Git just writes a 41-byte file), so branching per feature is normal. The default branch is `main` (older repos use `master`).

### Q14. What is `HEAD`?
`HEAD` is a pointer to where you are right now, usually to the current **branch**, which in turn points to its latest commit. When you commit, the branch that HEAD points to moves forward.

### Q15. How do you create, switch to and delete a branch?
```bash
git switch -c feature        # create and switch (classic: git checkout -b feature)
git switch main              # switch (classic: git checkout main)
git branch -d feature        # delete (safe, merged only)
git branch -D feature        # force delete
```

### Q16. What is the difference between `git clone` and `git init`?
`git init` starts a brand-new empty repo. `git clone <url>` copies an existing repo, with all history, and automatically sets up `origin` and checks out the default branch.

### Q17. What is a remote? What is `origin`?
A remote is a named link to another copy of the repository. `origin` is the default name Git gives to the remote you cloned from. It is only a nickname for a URL. Check with `git remote -v`.

### Q18. What is the difference between a commit and a push?
A **commit** saves a snapshot **locally** and needs no internet. A **push** uploads your local commits to a remote so others can see them. Committing does not share anything.

### Q19. What is the difference between `git fetch` and `git pull`?
`fetch` downloads new commits and updates remote-tracking branches such as `origin/main` but does **not** touch your files or local branch. `pull` is `fetch` plus a merge (or a rebase with `--rebase`) into your current branch.

### Q20. How do you set your name and email in Git?
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```
Every commit is stamped with these. Use `git config --list` to check. Without `--global`, the setting applies to that repo only.

---

## Level 2: Intermediate (Q21 to Q45)

### Q21. What is the difference between `git merge` and `git rebase`?
Both bring changes from one branch into another.
- **Merge** joins the two histories, adding a merge commit if the branches diverged. History stays exactly as it happened.
- **Rebase** replays your commits on top of another branch, giving a **linear** history, but it creates new commits with new hashes.

Rule: merge for shared/public branches, rebase to tidy your own local work.

### Q22. What is a fast-forward merge vs a three-way merge?
If the target branch hasn't moved since you branched, Git simply moves its pointer forward (**fast-forward**, no new commit). If both branches have new commits, Git creates a **merge commit** with two parents using a three-way merge (both tips plus their common ancestor). Use `--no-ff` to force a merge commit.

### Q23. What is a merge conflict and how do you resolve it?
It happens when both branches changed the same lines (or one deleted a file the other edited), so Git can't choose. Git marks the file with `<<<<<<<`, `=======` and `>>>>>>>`.
1. Edit the file and keep the right content, removing the markers.
2. `git add <file>`
3. `git commit` (or `git merge --continue`).

To cancel the merge: `git merge --abort`.

### Q24. Explain `git reset --soft`, `--mixed` and `--hard`.
All three move the branch pointer. They differ in what else they reset:

| Mode | Staging area | Working directory |
|---|---|---|
| `--soft` | kept (changes stay staged) | kept |
| `--mixed` (default) | reset (changes unstaged) | kept |
| `--hard` | reset | reset (**changes lost**) |

### Q25. What is the difference between `git reset` and `git revert`?
`reset` moves the branch back and **rewrites history**, so it's fine for local, unpushed work. `revert` adds a **new commit** that undoes an earlier one, leaving history intact, so it is **safe for pushed/shared commits**.

### Q26. How do you unstage a file or discard changes to it?
```bash
git restore --staged file.js    # unstage, keep the edits
git restore file.js             # discard unstaged edits (cannot be undone)
```
The older equivalents are `git reset HEAD file.js` and `git checkout -- file.js`.

### Q27. What does `git stash` do?
It shelves uncommitted changes and gives you a clean working directory so you can switch tasks.
```bash
git stash push -m "wip"   # save
git stash list            # view
git stash pop             # re-apply and remove
git stash apply           # re-apply and keep
git stash -u              # include untracked files
```

### Q28. Lightweight vs annotated tags?
A **lightweight** tag is just a name pointing at a commit. An **annotated** tag (`git tag -a v1.0 -m "msg"`) is a full object storing the tagger, date and message, and can be signed. Use annotated tags for releases. Tags are not pushed by default: `git push origin v1.0` or `git push origin --tags`.

### Q29. What does `git push -u origin branch` do?
It pushes the branch and sets its **upstream**, linking the local branch to `origin/branch`. Afterwards `git push` and `git pull` work with no arguments and `git status` can show ahead/behind counts.

### Q30. What are the different forms of `git diff`?
```bash
git diff                 # working directory vs staging area (unstaged changes)
git diff --staged        # staging area vs last commit (what you'll commit)
git diff HEAD            # working directory vs last commit (everything)
git diff main..feature   # between two branches/commits
```

### Q31. What is the difference between `rm`, `git rm` and `git rm --cached`?
- `rm file`: deletes from disk only; you still need to stage the deletion.
- `git rm file`: deletes from disk **and** stages the deletion.
- `git rm --cached file`: stops tracking the file but **keeps it on disk** (useful for a committed `.env`).

### Q32. What does `git mv` do?
It renames or moves a file and stages the change in one step. It equals `mv old new` followed by `git add old new`. Git detects renames by content similarity, and `git log --follow file` follows history across renames.

### Q33. `git branch -d` vs `-D`?
`-d` deletes only if the branch is fully merged. `-D` is `--delete --force` and deletes even unmerged work. You can't delete the branch you are on. To delete a remote branch: `git push origin --delete name`.

### Q34. What is the difference between `HEAD~1` and `HEAD^`? And `HEAD~2` vs `HEAD^2`?
`HEAD~n` goes back n commits along the first parent (`HEAD~2` = `HEAD^^`). `HEAD^n` picks the n-th **parent** of a merge commit, so `HEAD^2` is the second parent (the branch that was merged in). For normal commits `HEAD~1` and `HEAD^` are the same.

### Q35. How do you change the last commit (message or content)?
```bash
git commit --amend -m "Better message"      # change the message
git add forgotten.js && git commit --amend --no-edit   # add a file to the last commit
```
Amending creates a **new** commit hash, so only do it on commits you haven't pushed (or on your own branch, then force push).

### Q36. How do you view history in different ways?
```bash
git log --oneline                    # compact
git log --oneline --graph --all      # branch graph
git log -n 5                         # last 5
git log --author="Name" --since="2 weeks ago"
git log -p file.js                   # changes to one file
git log --follow file.js             # across renames
```

### Q37. How do you find who changed a line, and when a piece of code was introduced?
`git blame file` shows the last commit and author for every line (`-L 10,20` for a range). To find the commit that added or removed a string use the **pickaxe**: `git log -S "functionName"` (or `-G "regex"`).

### Q38. I added a file to `.gitignore` but Git still tracks it. Why?
`.gitignore` only affects **untracked** files. A file already committed stays tracked. Fix:
```bash
git rm --cached file
git commit -m "Stop tracking file"
```
For personal ignores that shouldn't be shared, use `.git/info/exclude` or a global ignore file.

### Q39. What is the difference between fork, clone and branch? What is a pull request?
- **Clone:** a local copy of a repo.
- **Fork:** a server-side copy of someone else's repo under your account (a GitHub/GitLab feature, not Git itself).
- **Branch:** a parallel line of work inside one repo.
- **Pull request (PR / MR):** a request to merge your branch into another, used for review and discussion. This is also a platform feature, not core Git.

Open-source flow: fork, clone, add `upstream` remote, branch, push to your fork, open a PR.

### Q40. How do you clean up stale remote-tracking branches?
`git fetch --prune` (or `git remote prune origin`) removes local references to branches that no longer exist on the remote. Enable it by default with `git config --global fetch.prune true`.

### Q41. Your push is rejected with "non-fast-forward" or "fetch first". What happened?
The remote has commits you don't have locally. Integrate them first:
```bash
git pull --rebase     # or: git pull
git push
```
Don't solve it with `--force`, which would overwrite your teammates' work.

### Q42. What is `git cherry-pick`?
It copies the changes of a specific commit onto the current branch as a **new commit** (new hash). Common use: apply a hotfix from `main` onto a release branch.
```bash
git cherry-pick abc123
```
If there's a conflict: fix, `git add`, then `git cherry-pick --continue` (or `--abort`).

### Q43. What is `git reflog` and why is it important?
The reflog is a **local** journal of every place `HEAD` and your branch tips have pointed, including commits no branch references any more. It is your safety net after a bad `reset --hard`, a deleted branch or a botched rebase.
```bash
git reflog
git reset --hard HEAD@{2}     # go back to where HEAD was 2 moves ago
```
Entries expire (about 90 days for reachable, 30 for unreachable) and it is never pushed.

### Q44. What is a detached HEAD?
It means `HEAD` points directly to a commit instead of a branch, for example after `git checkout abc123` or checking out a tag. Commits you make there belong to no branch and can be lost. To keep them: `git switch -c new-branch`.

### Q45. How do you delete untracked files?
```bash
git clean -n       # dry run: shows what would be deleted
git clean -f       # delete untracked files
git clean -fd      # also directories
git clean -fdx     # also ignored files
```
It is **irreversible**, so always run `-n` first.

---

## Level 3: Advanced (Q46 to Q66)

### Q46. How does Git store data internally?
Git is a **content-addressable** key-value store. Everything lives in `.git/objects` as one of four object types:
- **blob:** file contents
- **tree:** a directory (names pointing to blobs/trees)
- **commit:** points to a root tree, parent commit(s), author, committer and message
- **tag:** an annotated tag object

Git stores **snapshots** (not diffs), de-duplicating identical content, and later packs objects with delta compression.

### Q47. What is a SHA and how does Git guarantee integrity?
Each object is named by the hash of its content (SHA-1, 40 hex characters; SHA-256 is supported in newer setups). A commit's hash includes its parents' hashes, so changing any old content would change every later hash. That makes history tamper-evident, and it is why rewriting history produces new hashes.

### Q48. What is inside the `.git` directory?
`HEAD` (current branch), `config` (repo settings), `objects/` (all data), `refs/` (branches, tags, remote-tracking refs as files with hashes), `index` (the staging area), `hooks/` (sample scripts) and `logs/` (reflog data). A branch is literally a file in `refs/heads/` containing a commit hash.

### Q49. What can interactive rebase do?
`git rebase -i HEAD~4` opens a todo list where you can change each commit's action: `pick` (keep), `reword` (edit message), `edit` (stop to amend), `squash` (merge into previous, combine messages), `fixup` (merge into previous, discard message) and `drop` (delete). You can also reorder lines to reorder commits. It's the standard way to clean up a branch before opening a PR.

### Q50. What is the golden rule of rebase, and why?
**Never rebase commits that others have already pulled.** Rebase creates new commits with new hashes; teammates still have the old ones, so their histories diverge and you get duplicate commits and painful conflicts. Rebase your own unpublished (or private) branches only.

### Q51. What is `git push --force` and why is `--force-with-lease` safer?
`--force` overwrites the remote branch with your version, destroying any commits you haven't seen. `--force-with-lease` only overwrites **if the remote is still where you last saw it**; if someone else pushed in the meantime, it refuses.
```bash
git push --force-with-lease
```
Use it after rebasing or amending **your own** branch. Never force-push to shared branches like `main`.

### Q52. During a rebase, what do "ours" and "theirs" mean?
They are **reversed** compared with a merge. In a rebase, "ours" is the branch you are rebasing **onto** (upstream) and "theirs" is **your commit** being replayed. So `git checkout --theirs file` during a rebase keeps *your* version. During a merge, "ours" is your current branch.

### Q53. How does `git bisect` work?
It binary-searches history for the commit that introduced a bug. You mark one `bad` and one `good` commit, Git checks out the midpoint, you test and mark it, and it repeats. 1,000 commits take about 10 steps.
```bash
git bisect start
git bisect bad
git bisect good v1.0
# test, then: git bisect good | git bisect bad
git bisect reset
```
Automate with `git bisect run ./test.sh`.

### Q54. How do you revert a merge commit?
A merge commit has two parents, so you must say which side to keep:
```bash
git revert -m 1 <merge-commit-hash>
```
`-m 1` means "keep the first parent (the branch you merged into) as the mainline". Note: if you later want those changes back, you must revert the revert, because Git thinks they were already merged.

### Q55. What does `git rebase --onto` do?
It transplants a range of commits onto a new base.
```bash
git rebase --onto main feature-a feature-b
```
This takes the commits in `feature-b` that are **after** `feature-a` and replays them on `main`. It's useful when a branch was cut from another feature branch that has since been merged or dropped.

### Q56. What is the difference between `A..B` and `A...B`?
- For `git log`: `A..B` is commits in B not in A. `A...B` is commits in either but not both (symmetric difference).
- For `git diff`: `A..B` compares the two tips directly. `A...B` compares B against the **merge base** of A and B, i.e. "what B changed since it branched", which is exactly what a PR diff shows.

### Q57. What is the merge base?
The most recent common ancestor of two branches. Git uses it for three-way merges. Find it with `git merge-base A B`.

### Q58. What are Git hooks?
Scripts Git runs at certain events, kept in `.git/hooks`: `pre-commit` (lint/test before committing), `commit-msg` (validate message format), `pre-push` and so on. They are **not** versioned or copied on clone. Teams share them via `git config core.hooksPath .githooks` or tools like Husky and pre-commit.

### Q59. What are submodules and how are they different from subtrees?
A **submodule** embeds another repo at a **specific commit** inside yours, tracked in `.gitmodules`. Clone with `git clone --recurse-submodules`, or run `git submodule update --init --recursive`. A **subtree** copies the other project's files and history **into** your repo, so no extra commands are needed for others, but pushing changes back is harder. Submodules are explicit but easy to get out of sync.

### Q60. What is Git LFS?
Git Large File Storage replaces big binaries (videos, datasets, design files) in the repo with small text pointers and stores the real files on a separate server. It keeps clones fast. Use `git lfs track "*.psd"`. Plain Git handles large binaries badly because every version is stored in history.

### Q61. What is `git worktree`?
It lets you check out **multiple branches at once** in separate directories that share one `.git`. Handy for reviewing a PR or fixing a hotfix without stashing.
```bash
git worktree add ../hotfix hotfix-branch
git worktree list
git worktree remove ../hotfix
```

### Q62. What is `git rerere`?
"Reuse recorded resolution". When enabled (`git config rerere.enabled true`), Git remembers how you resolved a conflict and applies the same fix automatically if the same conflict appears again, for example when repeatedly rebasing a long-lived branch.

### Q63. What are signed commits?
Commits signed with a GPG or SSH key to prove who made them (`git commit -S`, or `git config commit.gpgsign true`). Hosts show a **Verified** badge. Without signing, anyone can set any name and email on a commit.

### Q64. How do you handle very large repositories?
- **Shallow clone:** `git clone --depth 1` (recent history only).
- **Partial clone:** `git clone --filter=blob:none` (download file contents on demand).
- **Sparse checkout:** `git sparse-checkout set dir/` (only some directories in the working tree).
- **Git LFS** for big binaries, and `git gc` to compact objects.

### Q65. What is a bare repository?
A repo with no working directory, just the contents of `.git`. Created with `git init --bare`, it is what servers use as the central remote you push to (the names usually end in `.git`). You can't edit files in it directly.

### Q66. Merge commit vs squash merge vs rebase merge on a PR: what are the trade-offs?
| Option | Result | Pros | Cons |
|---|---|---|---|
| Merge commit | All branch commits plus a merge commit | Full, true history | Noisy history |
| Squash and merge | One commit on main for the whole PR | Clean, one commit per feature, easy to revert | Loses individual commits |
| Rebase and merge | Branch commits replayed linearly, no merge commit | Linear history, keeps commits | Rewrites hashes, no merge marker |

Many product teams default to squash merge.

---

## Level 4: Real-world scenarios (Q67 to Q83)

### Q67. I committed to `main` by mistake instead of a feature branch. How do I fix it (not pushed yet)?
```bash
git branch feature-x        # new branch pointing at the commit
git reset --hard HEAD~1     # move main back one commit
git switch feature-x        # your commit is here
```
Stash or commit other uncommitted work first, because `--hard` discards it.

### Q68. How do I undo my last commit?
- Not pushed, keep the changes: `git reset --soft HEAD~1`
- Not pushed, throw the changes away: `git reset --hard HEAD~1`
- Already pushed: `git revert HEAD`, then push (don't rewrite shared history)

### Q69. How do I change the message of an older commit?
`git rebase -i HEAD~3`, change `pick` to `reword` for that commit, save and edit the message. If it was already pushed, you'd need `git push --force-with-lease`, which is only acceptable on your own branch.

### Q70. I deleted a branch or ran `reset --hard` and lost commits. Can I recover them?
Yes, if the work was committed. Use the reflog:
```bash
git reflog                           # find the hash or HEAD@{n}
git branch recovered <hash>          # recreate the branch
# or: git reset --hard HEAD@{1}
```
**Uncommitted** changes discarded by `reset --hard` or `restore` are not recoverable.

### Q71. I accidentally committed a password or API key (or a huge file). What do I do?
1. **Rotate/revoke the secret immediately.** Assume it is compromised, since pushing exposes it even if you delete it later.
2. Remove it from history with `git filter-repo` (or BFG Repo-Cleaner). A plain "delete it" commit leaves it in old commits.
3. Force-push the cleaned history and ask collaborators to re-clone.
4. Add the file to `.gitignore`.

### Q72. How do I make my local branch exactly match the remote, discarding local changes?
```bash
git fetch origin
git reset --hard origin/main
```
This destroys local commits and changes on that branch, so be sure first.

### Q73. A bad commit is already on `main` and in production. What's the safest fix?
`git revert <hash>` and push. This adds a new commit that undoes the change without rewriting history. For a merge commit use `git revert -m 1 <hash>`. Fix the problem properly on a branch afterwards.

### Q74. You're mid-feature and an urgent bug comes in. What do you do?
```bash
git stash push -m "wip feature"
git switch -c hotfix main
# fix, commit, push, open PR
git switch feature
git stash pop
```
Alternatively, use `git worktree` so you don't need to stash.

### Q75. `git pull` gives merge conflicts. Walk through your steps.
1. `git status` to see the conflicted files.
2. Open each one, resolve the markers, keep the correct code.
3. Run the tests.
4. `git add <files>`, then `git commit` (or `git rebase --continue` if you used `pull --rebase`).
5. If it goes wrong: `git merge --abort` or `git rebase --abort`.

### Q76. A bug appeared sometime in the last 200 commits. How do you find the cause?
Use `git bisect` between a known good and a bad commit (see Q53), ideally with `git bisect run` and a test script. For a specific suspicious change, `git log -S "text"` finds the commit that added or removed it.

### Q77. How do you find who deleted a line or when a function was introduced?
`git log -S "functionName" --oneline` lists commits that changed the number of occurrences of that string. `git log -L :functionName:file.js` shows the history of that function. `git blame` only shows the last change to *existing* lines.

### Q78. How do you commit only part of a file, or split one commit into two?
Part of a file: `git add -p` (stage hunk by hunk).
Split a commit: `git rebase -i HEAD~3`, mark it `edit`, then `git reset HEAD^`, stage and commit the pieces separately, and run `git rebase --continue`.

### Q79. I merged a branch locally but haven't pushed, and I want to undo it.
If you are still in conflict: `git merge --abort`. If the merge completed: `git reset --hard ORIG_HEAD` (`ORIG_HEAD` is where the branch was before the merge) or `git reset --hard HEAD~1`. If it's already pushed, use `git revert -m 1`.

### Q80. How do I take just one file from another branch?
```bash
git restore --source=feature -- path/to/file.js
# classic: git checkout feature -- path/to/file.js
```
This overwrites that file in your working directory with the version from `feature`, without merging anything else.

### Q81. Git doesn't track my empty folder. Why?
Git tracks **files**, not directories. Put a placeholder such as `.gitkeep` (any file) inside it and commit that.

### Q82. How do you deal with Windows/Mac line-ending problems (CRLF vs LF)?
Set `core.autocrlf` (`true` on Windows, `input` on macOS/Linux), or better, commit a `.gitattributes` with `* text=auto` so the whole team gets consistent behaviour.

### Q83. Two teammates edited the same file. How do you prevent painful conflicts?
Pull or rebase often so branches stay short-lived, keep commits and PRs small, agree on code ownership and formatting (auto-formatters), split large files, and communicate when touching shared code. Conflicts can't be avoided entirely, but they stay small when you integrate frequently.

---

## Level 5: Workflow and team practices (Q84 to Q95)

### Q84. Compare Git Flow, GitHub Flow and trunk-based development.
- **Git Flow:** long-lived `main` and `develop`, plus `feature/*`, `release/*` and `hotfix/*` branches. Good for scheduled, versioned releases; heavy for continuous delivery.
- **GitHub Flow:** `main` is always deployable; short-lived feature branch, PR, review, merge, deploy. Simple.
- **Trunk-based:** everyone integrates into `main` very frequently (branches live hours or a day or two), using **feature flags** to hide unfinished work. Favoured by many large product companies because it reduces merge pain and supports continuous deployment.

### Q85. Walk me through your daily Git workflow.
```bash
git switch main && git pull            # start from latest
git switch -c feature/login-validation
# code, then repeat:
git add -p && git commit -m "Validate email on login form"
git fetch origin && git rebase origin/main   # stay current
git push -u origin feature/login-validation  # open PR
# after review and merge:
git switch main && git pull
git branch -d feature/login-validation
```

### Q86. What makes a good commit and a good commit message?
Commits should be **atomic** (one logical change that builds and passes tests). Messages use a short imperative summary (about 50 characters), a blank line, then the "why" in the body. Many teams use **Conventional Commits** (`feat:`, `fix:`, `docs:`, `refactor:`) to automate changelogs and versioning.

### Q87. What do you look for in a code review, and how do you keep PRs reviewable?
Keep PRs small and focused with a clear description and linked issue. Reviewers check correctness, tests, readability, security and side effects. Platform features such as **branch protection** (required reviews, passing CI, no force-push to `main`) enforce the process. Note that these are GitHub/GitLab features, not Git itself.

### Q88. How do you keep a long-running feature branch up to date with `main`?
Regularly integrate: `git fetch origin` then either `git rebase origin/main` (for a private branch; follow with `git push --force-with-lease`) or `git merge origin/main` (for a shared branch). Smaller, more frequent syncs mean smaller conflicts.

### Q89. How do you ship a hotfix while a release is in progress?
Branch from the production tag or release branch (`git switch -c hotfix/1.2.1 v1.2.0`), fix and test, merge to the release/production branch and tag it, then **cherry-pick or merge** the fix into `main` (and `develop` in Git Flow) so it isn't lost.

### Q90. How do you version and mark releases?
Use **annotated tags** with semantic versioning: `MAJOR.MINOR.PATCH` (`git tag -a v1.4.2 -m "Release 1.4.2"` then `git push origin v1.4.2`). Breaking change bumps MAJOR, new feature bumps MINOR, bug fix bumps PATCH. Release tooling and CI usually trigger on tags.

### Q91. How does Git fit into CI/CD?
A push or PR triggers a webhook on the Git host, which starts a pipeline (build, lint, tests, security scans). Results appear as **status checks** on the PR, and branch protection can block merging until they pass. Merges to `main` or new tags trigger deployment.

### Q92. Monorepo vs polyrepo: what are the trade-offs?
A **monorepo** keeps many projects in one repo: easy atomic cross-project changes, shared tooling and a single source of truth, but it needs scalability tooling (sparse checkout, partial clone, build caching, ownership rules). **Polyrepo** gives each service its own repo: clear boundaries and independent permissions and releases, but cross-repo changes and dependency versioning are harder.

### Q93. Your repository is slow to clone or operate. What do you do?
Use shallow or partial clones (`--depth`, `--filter=blob:none`), sparse checkout, move large binaries to Git LFS, run `git gc`, remove accidentally committed large files from history with `git filter-repo`, and consider splitting the repo if it has outgrown one team.

### Q94. SSH vs HTTPS remotes?
**HTTPS** (`https://github.com/user/repo.git`) works everywhere but needs a token or credential helper, since password logins are no longer accepted by major hosts. **SSH** (`git@github.com:user/repo.git`) uses your key pair, so no repeated prompts once the public key is added to your account. Switch with `git remote set-url origin <new-url>`.

### Q95. What is `origin/main` and how is it different from `main`?
`main` is your **local** branch. `origin/main` is a **remote-tracking branch**: a read-only local bookmark showing where `main` was on `origin` the last time you fetched or pushed. `git fetch` updates it; `git pull` then merges it into `main`. "Your branch is ahead of 'origin/main' by 2 commits" means you have two commits you haven't pushed.

---

## Last-minute checklist: type these from memory

```bash
git init / clone              git status / log --oneline --graph
git add -p / commit -m        git diff / diff --staged
git switch -c / branch -d     git merge / rebase / cherry-pick
git fetch / pull --rebase     git push -u / --force-with-lease
git stash / stash pop         git restore / restore --staged
git reset --soft|--mixed|--hard    git revert / revert -m 1
git commit --amend            git reflog
git tag -a                    git bisect start/good/bad/reset
git blame / log -S            git clean -n / -fd
```

If you can explain **why** each command exists (not just what it does) and when it is **unsafe**, you are ready for most Git interview rounds.
