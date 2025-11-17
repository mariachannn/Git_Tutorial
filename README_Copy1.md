# Git Guide
## Install Git
Go to the Git website https://git-scm.com/install/windows and download the correct installer for your system.
## Set user.name/ user.email
To set your Git username and email for every repository on your laptop. You can use your GitHub-provided noreply email address or any email address that is **linked to your GitHub account.** Remember to toggle on "Keep my email addresses private" if you are using your private email and would like to hide it
```bash
git config --global user.name "Your Name"
git config --global user.email "youremail@domain.com"
```
Check if the setting is correct
```bash
git config --global user.name
git config --global user.email
```
```Output
Your Name
youremail@domain.com
```

## Clone a repo (HTTPS)
```bash
git clone https://github.com/abichoi/Git_Tutorial.git

# or specify destination path
git clone https://github.com/abichoi/Git_Tutorial.git <an/empty/destination/foldr/path>

```

## Use SSH keys to clone/push
### Create a ssh key
```bash
ssh-keygen -t rsa -b 4096 -C "youremail@domain.com"
```
When prompted, you can specify where to save the key or press "Enter" to save it to the default location ('~/.ssh/id_rsa')

### Add SSH key to ssh-agent
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa #change the address to where your ssh key is
```

### Add SSH key to yuor GitHub
1. Go to your GitHub setting page
2. Under SSH and GPG keys session, click on New SSH key
3. Copy your public key from your id_rsa.pub file and paste it on to Github and SAVE it.

### Clone repo using SSH
```bash
git clone git@github.com:Dkaka/COMP0219_Tutorials.git
```
## Stage/commit with meaningful messages
```bash
#cd to where your repo is cloned to
cd <path/of/cloned/repo>
git status
git add path/to/file            
git add -p                      
git commit -m "feat: add readme file"
git push
```

## Create/switch branches (switch/checkout)
```bash
# create a new branch (change new_branch_name to your desired name)
git checkout -b new_branch_name
#switch to your new branch
git switch new_branch_name
#push the new branch to the repo
git push origin new_branch_name
```

## Merge and resolve a simple conflict
```bash
# To create a change in the new branch, we will duplicate the readme file
cp -R README.md README_Copy2.md
git add READ_Copy2.md
git commit -m "add read me copy 2"
# This command ensures that your branch can only be updated when the changes can be applied as a fast-forward (ff). If ff is not possible, the command will fail.
git pull --ff-only

git switch main
git merge new_branch_name

# push if there is no conflict
git push
```
### If conflicts happen
1. Edit the confliced files and edit them
2. Then:
```bash
git add <conflicted-file>
git commit
git push
```

## Rebase my branch onto main safely
This takes th commits that exsit on new_branch_name but not on main, and put them on top of the main. 

Let's say the orginal state of the repo is like this:

```
main: A---B---C
             \
new_branch_name:   D---E
```
Then your groupmate updated the main branch
```
main: A---B---C---F---G
             \
new_branch:   D---E
```

After running the code below, you will get this:
```
main: A---B---C---F---G
                     \
new_branch:           D'---E'
```

```bash
git checkout new_branch_name
git rebase main

## If conflicts happen, edit the confliced files, then:
git add <fixed-file>
git rebase --continue
## If you need to back out:
git rebase --abort
# Force-with-lease updates your remote branch safely:
git push --force-with-lease
```

## Open a Pull/Merge Request and request review
1. Push your branch
```bash
git push -u origin new_branch_name
```
2. Open a Pull/Merge Request in the "Pull requests" tab in the repo by clicking the "Create Pull Request" button
3. Select which branch you want to pull from and to
4. Filled in everything and click "Create pull request"

## Use .gitignore effectively
```bash
# Create a gitignore file
touch .gitignore

# Create a example file to ignore
touch secret.py
```
Then, open .gitignore in an editor you prefer and add the following into .gitignore

```bash
secret.py
# if you want git to ignore a folder you can add
secret_folder/
```

## Undo safely (revert, not reset --hard on shared history)
### git reset -- Rewrite/ move history
git reset moves your branch's HEAD, and possibly updates the stage (index) and working tree.
Example:
```bash
# Uncommit but keep changes staged (i.e. your changes won't be lost and is ready to be commited again)
git reset --soft HEAD~1
```
### git restore -- Safely undo changes to files
git restore was introduced to make Git safer and reduce accidental data loss.
Example:
```bash
# Unstage a file but keep changes 
git restore --staged path/to/file
# Discard local changes in a file
git restore path/to/file
```
### git revert -- Safely undo committed history
git revert creates a new commit that undoes the changes from a previous commit.
Example:
```bash
# Revert a commit
git revert <commit-sha>
```

## Stash and apply work
git stash is used to save changes that are not ready to be committed
```bash
# Add your changes to the current branch 
git switch new_branch_name
git add .
# Stash your changes
git stash
# Switch to another branch (we use the main branch here)
git checkout main
# Edit the main branch by duplicating a file and commit the edit
cp -R README.md README_Copy7.md
git add .
git commit -m "Fix the bug in main"
git push main
# Switch back to original branch
git checkout new_branch_name
# Retrieve your stashed changes
git stash pop
git commit -m "add copy 6"
git push 
```

## Inspect history (log, show, blame)
```bash
# List all the commits made in the repo
git log # type 'q' to exit the current screen
# List all the commits and show the difference introducted in each commit in a specific file
git log -p path/to/file
# Show the most recent commit on the current branch
git show
# Show a specific commit        
git show <commit-sha>
# Show the author name and commit information of each line of a specific file
git blame path/to/file     
```            

## Use tags/releases
```bash
# List out all the tags
git tag 
# Create a tag and specify a tagging message using -m
git tag -a v0.1 -m "Tutorial"
# Show the tag data
git show v0.1
# By default, git push command doesn't transfer tags to GitHub. You have to specificly push the tags to Github by:
git push origin v0.1
#or use --tags if you have a lot of tags to push all at once
git push origin --tags
```

## Submodule vs Subtree
Submodule links two repo and subtree merge two repo.
Git submodule lets you to include a submodule within your repo, where the submodule is a refernece to a specific commit in another repo.
Git Subtree lets you include a subdirectory within another repo, while still being able to push and pull changes to and from the subtree's repository. 

### Handle submodules
A git submodule is a repo embedded inside another repo. 
```bash
# Add the submodule
git submodule add <submodule-repo-url><path-the directory-where-the-submodule should-be-added>
# initialise and fetch the submodule
git submodule update --init
```
If you want to clone a repo with submodule:
```bash
git clone --recurse-submodules <repository-url>
```

To commit changes in submodules
```bash
cd <submodule-path>
# make changes and commit
git add.
git commit -m "Changed submodule content"
```

### Handle subtrees
Git Subtree is used to include a repo as a subdirectory within another repo.

```bash
# Add a subtree to parent repository
git remote add remote-name https://github.com/abichoi/Git_Tutorial_sub_repo.git
# Clone https://github.com/abichoi/Git_Tutorial_sub_repo.git into subtreeDirectory folder
git subtree add --prefix=subtreeDirectory https://github.com/abichoi/Git_Tutorial_sub_repo.git main --squash

# Pull changes from the subtree repo to subtreeDirectory
git subtree pull --prefix subtreeDirectory https://github.com/abichoi/Git_Tutorial_sub_repo.git master --squash
# Push changes from the subtreeDirectory to the subtree repo
git subtree push --prefix subtreeDirectory https://github.com/abichoi/Git_Tutorial_sub_repo.git master
```
