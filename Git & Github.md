### what is Git-
**Git** is a distributed version control system (VCS) that helps developers track and manage changes to their code over time. It allows multiple developers to work on the same codebase simultaneously and keeps a history of changes, making it easy to revert to previous versions if needed.

#### Key Features of Git:

- **Version Control**: Tracks changes in files, allowing developers to save different versions of code, known as commits.
- **Branching**: Enables developers to create isolated branches for new features or bug fixes, keeping the main codebase clean.
- **Merging**: Combines changes from different branches, which is essential for collaboration and feature integration.
- **Distributed System**: Every developer has a full copy of the codebase, which allows for offline work and reduces reliance on a single server.


### What is GitHub?

**GitHub** is a cloud-based platform that hosts Git repositories, making it easy to share code, collaborate, and manage projects. GitHub provides a web-based interface for working with Git, along with additional features like issue tracking, pull requests, and access controls.

#### Key Features of GitHub:

- **Remote Repository Hosting**: Stores your code in the cloud, accessible from anywhere.
- **Collaboration**: Developers can work together on projects, review each other’s code, and manage project tasks.
- **Pull Requests**: A feature that allows developers to propose changes, review code, and discuss modifications before merging them into the main codebase.
- **Issues**: Built-in bug tracking and task management to help organize and prioritize development work.
- **GitHub Actions**: Provides automation for CI/CD workflows directly in GitHub.

# Configure Git with Username and Email-

#### 1)$ git config --global user.name "Paul Philips
**What it does:**

- Sets your **name** to "Paul Philips" for all the commits you make in any Git repository on your computer.

**Why it’s useful:**

- When you make a commit, Git will associate your **name** with that commit. This is especially important when you're collaborating with others and want to keep track of who made each change.

**Example Scenario:** You are working on a project with others, and when you make a change and commit it, your name "Paul Philips" will show up in the commit history.


#### 2)$ git config --global user.email paulphilips@email.com
**What it does:**

- Sets your **email** for all commits, so when you push changes to a repository (e.g., GitHub), your email will be associated with those changes.

**Why it’s useful:**

- Your email helps identify you in the commit history, and on platforms like GitHub, it will be used to link your commits to your account.

**Example Scenario:** When you push code to GitHub, your email (e.g., `paulphilips@email.com`) will show who made the changes, and it helps you keep track of contributions in a public repository.


#### 3)$ git config --list

**What it does:**

- Displays all the Git configuration settings that are currently applied. This includes your **user.name**, **user.email**, and any other configurations, like your preferred text editor or merge tool.

**Why it’s useful:**

- It lets you check if the user information you've set is correct and if any other Git settings are applied. You can also verify if there are conflicting settings between global and local repositories.

# Creating Local Git Repo
$ git init


# What is Git Commit?
A git commit is a command in Git that captures a snapshot of the project's currently staged changes, creating a permanent record in the repository's history.

- To check old files from commits
-

# Negative Cases
## If we made any change by mistake and save it

  

#Case1: To undo changes, get the last successful change

1) git restore . or filename (. mean all files)

  
[]()
#Case2: If we added the changes using git add then..

1) git restore --staged <file_path> # To unstage
2) git restore <file_path> # To discard changes in the working directory

#Case3: Added changes to staging area (didn't commit) after this added more changes to file

//To get the staged changes

1) git restore --worktree index.html

  

#Case4: How about if we did commit also wrong files

1) git reset --soft HEAD^ (uncommit and keep the changes)

2) git reset --hard HEAD^ (uncommit and discard the changes)



# git-checkout
### 1. Switching Branches

- **Command**: `git checkout <branch-name>`
- **Purpose**: Switches to an existing branch.
- **Example**:
    `git checkout main`

### 2. Creating and Switching to a New Branch

- **Command**: `git checkout -b <new-branch-name>`
- **Purpose**: Creates a new branch and switches to it.
- **Example**:
    `git checkout -b feature-branch`
### 3.Undoing Changes to Tracked Files

- **Command**: `git checkout -- <file>`
- **Purpose**: Discards changes in a file, restoring it to the last committed state.
- **Example**:
    `git checkout -- <file_name>`

### 4.restore a specific file (`<filename>`) to its state in the `master` branch.
 **Command**: `git checkout master -- <file_namegit >`
- **Purpose**: This is helpful if you've made changes to a file in your current branch and want to discard those changes, resetting it to the last committed state in the `master` branch.
- **Example**:
    `git checkout master -- <index.html>`



# git show
### 1.View a Specific Commit
- **Command**: `git show <commit-hash>`
- **Purpose**: Displays details about a specific commit using its unique hash.
### 2. Show Changes to a Specific File in a Commit

- **Command**: `git show <commit-hash> -- <filename>`
- **Purpose**: Limits the output to show changes only in the specified file for that commit.
- **Example**
    `git show a1b2c3d4 -- README.md`


# git log
### 1.View a Limited Number of Commits

- **Command**: `git log -n <number>`
- **Purpose**: Limits the output to the most recent `<number>` of commits.
- **Example**:
    `git log -n 5`
    
- **Output**: Shows only the 5 most recent commits.

### 2.Oneline Format

- **Command**: `git log --oneline`
- **Purpose**: Shows a simplified log, with each commit displayed in a single line (short hash and commit message).
- **Example**:
    `git log --oneline`

### 3.Graph Format

- **Command**: `git log --graph --oneline --all`
- **Purpose**: Displays a visual representation of the branch and merge history, useful for understanding commit relationships.
- **Example**:

    `git log --graph --oneline --all`

### 4.Filtering by Author
    
- Command**: `git log --author="<author-name>"`
- **Purpose**: Shows commits made by a specific author.
- **Example**:
    `git log --author="Jayesh"`
        
### 5.Filtering by Date
    
- **Command**: `git log --since=<date> --until=<date>`
 - **Purpose**: Limits commits to those made within a specified date range.
 - **Example**:
    `git log --since="2024-01-01" --until="2024-12-31"`

  #ProTip 
  **Combining Options**: Combine options to get customized views (e.g., `git log --author="Jayesh" --oneline --since="2024-01-01"`).


### 6.Customized Output Format

- **Command**: `git log --pretty=format:"<format-specifiers>"`
- **Purpose**: Allows you to format the log output with placeholders for specific information.
- **Example**:
    `git log --pretty=format:"%h - %an, %ar : %s"`
    
- **Placeholders**: `%h` for short hash, `%an` for author name, `%ar` for relative date, `%s` for commit message.

### Most Useful Placeholders

1. **Commit Information**
    
    - `%h`: Abbreviated commit hash
    - `%H`: Full commit hash
2. **Author Information**
    
    - `%an`: Author name
    - `%ae`: Author email
    - `%ad`: Author date (in a short, human-readable format)
    - `%ar`: Author date, relative (e.g., "2 weeks ago")
3. **Committer Information**
    
    - `%cn`: Committer name (if you need to see who actually committed, as opposed to authored)
    - `%cd`: Committer date (in a short, human-readable format)
4. **Message Information**
    
    - `%s`: Commit subject (first line of the commit message)
    - `%b`: Full body of the commit message (useful if you want to see all details in the message)
    - `%d`: Ref names (shows branch or tag names associated with the commit)
5. **Other Useful Options**
    
    - `%C(color)`: Custom coloring (e.g., `%C(red)%s%Creset` to color the subject red)
    - `%n`: Newline (to organize multi-line formats easily)

### 7.Viewing Changes in Each Commit

- **Command**: `git log -p`
- **Purpose**: Shows the changes (diffs) introduced in each commit.
- **Example**:
    `git log -p -2`
    
- **Output**: Displays detailed changes for the last two commits.

### 8.To view summary of change in commit

- **Command**: `git log --stat`
- **Purpose**: provides a summary of changes in each commit, showing the number of lines added and removed for each file.
- **Example**:
    `git log --stat`
    
- **Output**: Displays detailed changes for the last two commits.

  ### Useful Options with `--stat`
  
- **Limit Number of Commits**: You can combine `--stat` with `-n` to limit the output to a specific number of commits.

	`git log -n 3 --stat`
    
- **Oneline and Stat Combined**: To get a more concise view with just commit messages and stats, combine `--oneline` with `--stat`.

    `git log --oneline --stat`

### 9.search the commit history for a specific string or code

- **Command**: `git log -S "<search_string>"`
- **Purpose**: Finds commits that introduce or remove a specific string in the code.
- **Example**:
    `git log -S "index()"`
    
- **Output**: Displays at which commit index() added in file .

### 10.search for commits with messages containing a specific keyword or pattern

- **Command**
    `git log --grep="<search-pattern>"`
    
- **Purpose**: Finds commits where the commit message contains the specified pattern or keyword.

- **Example Usage**
  `git log --grep="bug fix"`
  This command will display all commits with messages that include "bug fix."


  ### Additional Options with `--grep`

1. **Combine with Multiple Grep Patterns**
    
    - **Command**:
        `git log --grep="fix" --grep="error"`
        
    - **Purpose**: Matches commit messages containing either "fix" or "error".
2. **Match All Patterns with `--all-match`**
    
    - **Command**:
        `git log --grep="fix" --grep="login" --all-match`
        
    - **Purpose**: Matches only commit messages that contain _both_ "fix" and "login".



# git push

what is git-



# Configure Git with Username and Email-

1)$ git config --global user.name "Paul Philips
2)$ git config --global user.email paulphilips@email.com

3)$ git config --list

# Creating Local Git Repo
$ git init


# What is Git Commit?
A git commit is a command in Git that captures a snapshot of the project's currently staged changes, creating a permanent record in the repository's history.

- To check old files from commits
-

# Negative Cases
## If we made any change by mistake and save it

  

#Case1: To undo changes, get the last successful change

1) git restore . or filename (. mean all files)

  

#Case2: If we added the changes using git add then..

1) git restore --staged <file_path> # To unstage
2) git restore <file_path> # To discard changes in the working directory

#Case3: Added changes to staging area (didn't commit) after this added more changes to file

//To get the staged changes

1) git restore --worktree index.html

  

#Case4: How about if we did commit also wrong files

1) git reset --soft HEAD^ (uncommit and keep the changes)

2) git reset --hard HEAD^ (uncommit and discard the changes)



# git-checkout
### 1. Switching Branches

- **Command**: `git checkout <branch-name>`
- **Purpose**: Switches to an existing branch.
- **Example**:
    `git checkout main`

### 2. Creating and Switching to a New Branch

- **Command**: `git checkout -b <new-branch-name>`
- **Purpose**: Creates a new branch and switches to it.
- **Example**:
    `git checkout -b feature-branch`
### 3.Undoing Changes to Tracked Files

- **Command**: `git checkout -- <file>`
- **Purpose**: Discards changes in a file, restoring it to the last committed state.
- **Example**:
    `git checkout -- <file_name>`

### 4.restore a specific file (`<filename>`) to its state in the `master` branch.
 **Command**: `git checkout master -- <file_namegit >`
- **Purpose**: This is helpful if you've made changes to a file in your current branch and want to discard those changes, resetting it to the last committed state in the `master` branch.
- **Example**:
    `git checkout master -- <index.html>`



# git show
### 1.View a Specific Commit
- **Command**: `git show <commit-hash>`
- **Purpose**: Displays details about a specific commit using its unique hash.
### 2. Show Changes to a Specific File in a Commit

- **Command**: `git show <commit-hash> -- <filename>`
- **Purpose**: Limits the output to show changes only in the specified file for that commit.
- **Example**
    `git show a1b2c3d4 -- README.md`


# git log
### 1.View a Limited Number of Commits

- **Command**: `git log -n <number>`
- **Purpose**: Limits the output to the most recent `<number>` of commits.
- **Example**:
    `git log -n 5`
    
- **Output**: Shows only the 5 most recent commits.

### 2.Oneline Format

- **Command**: `git log --oneline`
- **Purpose**: Shows a simplified log, with each commit displayed in a single line (short hash and commit message).
- **Example**:
    `git log --oneline`

### 3.Graph Format

- **Command**: `git log --graph --oneline --all`
- **Purpose**: Displays a visual representation of the branch and merge history, useful for understanding commit relationships.
- **Example**:

    `git log --graph --oneline --all`

### 4.Filtering by Author
    
- Command**: `git log --author="<author-name>"`
- **Purpose**: Shows commits made by a specific author.
- **Example**:
    `git log --author="Jayesh"`
        
### 5.Filtering by Date
    
- **Command**: `git log --since=<date> --until=<date>`
 - **Purpose**: Limits commits to those made within a specified date range.
 - **Example**:
    `git log --since="2024-01-01" --until="2024-12-31"`

  #ProTip 
  **Combining Options**: Combine options to get customized views (e.g., `git log --author="Jayesh" --oneline --since="2024-01-01"`).


### 6.Customized Output Format

- **Command**: `git log --pretty=format:"<format-specifiers>"`
- **Purpose**: Allows you to format the log output with placeholders for specific information.
- **Example**:
    `git log --pretty=format:"%h - %an, %ar : %s"`
    
- **Placeholders**: `%h` for short hash, `%an` for author name, `%ar` for relative date, `%s` for commit message.

### Most Useful Placeholders

1. **Commit Information**
    
    - `%h`: Abbreviated commit hash
    - `%H`: Full commit hash
2. **Author Information**
    
    - `%an`: Author name
    - `%ae`: Author email
    - `%ad`: Author date (in a short, human-readable format)
    - `%ar`: Author date, relative (e.g., "2 weeks ago")
3. **Committer Information**
    
    - `%cn`: Committer name (if you need to see who actually committed, as opposed to authored)
    - `%cd`: Committer date (in a short, human-readable format)
4. **Message Information**
    
    - `%s`: Commit subject (first line of the commit message)
    - `%b`: Full body of the commit message (useful if you want to see all details in the message)
    - `%d`: Ref names (shows branch or tag names associated with the commit)
5. **Other Useful Options**
    
    - `%C(color)`: Custom coloring (e.g., `%C(red)%s%Creset` to color the subject red)
    - `%n`: Newline (to organize multi-line formats easily)

### 7.Viewing Changes in Each Commit

- **Command**: `git log -p`
- **Purpose**: Shows the changes (diffs) introduced in each commit.
- **Example**:
    `git log -p -2`
    
- **Output**: Displays detailed changes for the last two commits.

### 8.To view summary of change in commit

- **Command**: `git log --stat`
- **Purpose**: provides a summary of changes in each commit, showing the number of lines added and removed for each file.
- **Example**:
    `git log --stat`
    
- **Output**: Displays detailed changes for the last two commits.

  ### Useful Options with `--stat`
  
- **Limit Number of Commits**: You can combine `--stat` with `-n` to limit the output to a specific number of commits.

	`git log -n 3 --stat`
    
- **Oneline and Stat Combined**: To get a more concise view with just commit messages and stats, combine `--oneline` with `--stat`.

    `git log --oneline --stat`

### 9.search the commit history for a specific string or code

- **Command**: `git log -S "<search_string>"`
- **Purpose**: Finds commits that introduce or remove a specific string in the code.
- **Example**:
    `git log -S "index()"`
    
- **Output**: Displays at which commit index() added in file .

### 10.search for commits with messages containing a specific keyword or pattern

- **Command**
    `git log --grep="<search-pattern>"`
    
- **Purpose**: Finds commits where the commit message contains the specified pattern or keyword.

- **Example Usage**
  `git log --grep="bug fix"`
  This command will display all commits with messages that include "bug fix."


  ### Additional Options with `--grep`

1. **Combine with Multiple Grep Patterns**
    
    - **Command**:
        `git log --grep="fix" --grep="error"`
        
    - **Purpose**: Matches commit messages containing either "fix" or "error".
2. **Match All Patterns with `--all-match`**
    
    - **Command**:
        `git log --grep="fix" --grep="login" --all-match`
        
    - **Purpose**: Matches only commit messages that contain _both_ "fix" and "login".


# git push

### *Basic Syntax*
`git push <remote> <branch>`

- **`<remote>`**: The name of the remote repository (e.g., `origin`).
- **`<branch>`**: The branch you want to push your changes to (e.g., `main` or `master`).
  eg. `git push origin main`
  
### 1) ***Push a new branch to remote***-  If you're pushing a branch that doesn’t exist on the remote yet, you can use:

bash

Copy code

`git push -u origin <new-branch-name>`


### 2)**Force push** A **force push** (`git push --force`) should be used cautiously because it overwrites history on the remote branch.

`git push --force origin main`


### 3) **Push all branches** If you want to push all branches, you can use

`git push --all origin`


### **Handling Errors**

1. **Rejection due to non-fast-forward update**
    
    - This happens if your local branch is behind the remote branch.
    - To fix this, you need to pull the latest changes using:
        
        bash
        
        Copy code
        
        `git pull origin <branch-name>`
        
    - Resolve any conflicts, commit the changes, and then push again.
    
#ProTip 
      1)  Always **pull** before pushing to ensure you're not overwriting anyone else's changes.
      2)Consider using branches to organize features or fixes, and then merge them into the main    branch later.


# **Git Branching and Merging Notes**

### **Branching in Git**

A **branch** is a separate line of development in Git. Branches are often used to work on a feature, bugfix, or experiment without affecting the main project (usually the `main` or `master` branch).

#### **Creating a Branch**

1) To create a new branch, use the following command:
`git branch <branch-name>`

This will create the branch, but won't switch to it.

2) To switch to the newly created branch, use:
`git checkout <branch-name>`

3) create and switch to a branch in one step:
`git checkout -b <branch-name>`

#### **Listing Branches**

1) To list all the branches in your local repository:
`git branch`

2) To see both local and remote branches:
`git branch -a`




# **Merging in Git**

Merging is the process of integrating changes from one branch into another. It is commonly used to combine work from feature branches back into the `main` branch.

#### **Basic Merge**

To merge changes from one branch into the current branch:
`git merge <branch-name>`

- Git will attempt to merge changes automatically.
- If there are no conflicts, the changes from `<branch-name>` are merged into the current branch.


#### **Merge Conflicts**

Sometimes, Git cannot automatically merge changes because there are conflicting changes in the same part of the same file. These conflicts must be resolved manually.

- Git will mark the conflicting areas in the file, and you will need to edit it.
- After resolving conflicts, mark the file as resolved:
    `git add <conflicted-file>`
    
- Once all conflicts are resolved, commit the merge:
    `git commit`

#ProTip Use VS code for more visualized comparison


### **Example Workflow**

1. Create a feature branch:
    `git checkout -b feature/new-UI`
    
2. Work on your changes and commit them:
    `git add . 
    `git commit -m "Add new UI`
    
3. Pull the latest changes from `main` to ensure you're up to date:
    `git checkout main 
    `git pull origin main`
    
4. Switch back to your feature branch and merge `main` into it:
    `git checkout feature/new-UI git merge main`
    
5. Resolve any conflicts, if any, then commit the changes.
    
6. Once your feature is complete and tested, merge it into `main`:
    `git checkout main
   ` git merge feature/new-UI`
    
7. Push the changes to the remote repository:
    `git push origin main`




### **.gitignore**

- **Avoid tracking unnecessary files**: Keeps the repository clean by not tracking files that are generated locally (e.g., logs, IDE settings, etc.).
- **Improve performance**: Reduces the amount of data being tracked and transferred.
- **Security**: Prevents sensitive files (e.g., API keys, passwords) from being accidentally pushed to the remote repository.
### *Basic Syntax*

1. **Ignore files by name**:
    - To ignore a specific file, simply add its name to ==`.gitignore `== file
     `secrets.txt`
    
2. **Ignore directories**:
    - To ignore a directory, add a trailing slash:
     `logs/`
    
3. **Wildcards and Patterns**:
    
    - `*` matches any string of characters.
    - `?` matches a single character.
    - `**/` matches directories at any level.
eg.
    ``*.log           # Ignore all `.log` files.
     `*.tmp           # Ignore all `.tmp` files. 
     `build/          # Ignore the `build ` directory. **/node_modules/ # Ignore `node_modules` directories at any level.`
    
4. **Negation**:
    
    - If you want to unignore a file that was previously ignored, use a `!` in front of the pattern.
    - 
    ``!important.txt  # Track `important.txt` even if the pattern `*.txt` is ignored.``


# **git clean**
used to remove untracked files and directories from your working directory in Git. It’s useful when you want to get rid of files that are not being tracked by Git, such as build artifacts or temporary files.


|**Option**|**Description**|**Example**|
|---|---|---|
|`git clean -n`|**Dry Run**: Shows which files would be deleted but doesn't actually remove anything.|`git clean -n`|
|`git clean -f`|**Force**: Removes untracked files (requires `-f` to actually delete files).|`git clean -f`|
|`git clean -fd`|Removes untracked **files and directories**.|`git clean -fd`|
|`git clean -fX`|Removes **only ignored files** (files listed in `.gitignore`).|`git clean -fX`|
|`git clean -fx`|Removes **both ignored and untracked files**.|`git clean -fx`|
|`git clean -i`|**Interactive Mode**: Lets you interactively select which files to remove.|`git clean -i`|
|`git clean -f <file>`|Removes specific untracked file(s) or directory.|`git clean -f <file1>`|
|`git clean -d`|Removes **untracked directories** (requires `-f` to remove them).|`git clean -fd`|
|`git clean -n -d`|Dry run that shows which untracked files and directories would be deleted.|`git clean -n -d`|




### **Git Tagging Notes**

In Git, **tags** are used to mark specific points in history, often for releases or other significant milestones in the project. Tags are like bookmarks that point to particular commits.


| **Command**                                        | **Description**                                                             | **Use Case**                                                                      |
| -------------------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| `git tag <tag-name>`                               | Creates a lightweight tag pointing to the current commit.                   | Use when you want to mark a commit with a simple tag without extra metadata.      |
| `git tag -a <tag-name> -m "message"`               | Creates an annotated tag with a message and metadata (author, date, etc.).  | Use when you want to add more information about the tag (e.g., release notes).    |
| `git tag -a <tag-name> <commit-hash> -m "message"` | Create an annotated tag on a specific commit by providing the commit hash.  | Use when you want to tag a commit that is not the latest commit.                  |
| `git tag`                                          | Lists all tags in the repository.                                           | Use when you want to see all existing tags in the repository.                     |
| `git show <tag-name>`                              | Displays the details of a tag (commit it points to, message, etc.).         | Use to view information about a specific tag, including the commit it points to.  |
| `git push origin <tag-name>`                       | Pushes a specific tag to the remote repository.                             | Use when you want to push a single tag to the remote server.                      |
| `git push --tags`                                  | Pushes all local tags to the remote repository.                             | Use when you want to push all tags to the remote server at once.                  |
| `git push --delete origin <tag-name>`              | Deletes a tag from the remote repository.                                   | Use when you need to remove a tag from the remote server.                         |
| `git tag -d <tag-name>`                            | Deletes a tag locally from your repository.                                 | Use when you want to remove a tag from your local repository.                     |
| `git checkout <tag-name>`                          | Checks out the specific commit associated with a tag (detached HEAD state). | Use when you want to inspect or work with the project at a specific tag's commit. |
| `git checkout -b <new-branch-name> <tag-name>`     | Creates a new branch from a specific tag.                                   | Use when you want to start working from a specific tag's point.                   |

---

### **Examples of Usage**

1. **Create an Annotated Tag**:
    `git tag -a v1.0 -m "First release"`
    
2. **Push a Tag to Remote**:
    `git push origin v1.0`
    
3. **List All Tags**:
    `git tag`
    
4. **Delete a Local Tag**:
    `git tag -d v1.0`
    
5. **Checkout a Tag**:
    `git checkout v1.0`