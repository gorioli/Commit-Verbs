The proposal based on this article: 
[medium.com/commit-verbs-101-why-i-like-to-use-this-and-why-you-should-also-like-it](https://medium.com/@danielfeelfine/commit-verbs-101-why-i-like-to-use-this-and-why-you-should-also-like-it-d3ed2689ef70)  

Commit verbs (that cover 98% of all use cases) 
- **Add** some feature 
- **Remove** class whatever 
- **Update** dependency x 
- **Refactor** function y
- **Fix** crazy bug  

For JIRA support it should be in the format: 
```[FKA-XXXX] Add functionality xyz```
  
## Some additional notes _(that can help to follow the proposal described in the link above)_  

It’s always better to avoid `merge` commits because they do not add any informational value to the commit history. 
To do so, before you `push` your branch: 
- do `pull`/`fetch` on your base branch (in case of the base branch was updated), 
- do `$ git rebase [base_branch_name]` instead of `merge`, so the base branch's commits will be applied before feature branch's commits. (This will avoid `merge` commit entirely).   

The `rebase` is safe if your new commits were not pushed to the origin. 
It’s still safe after commits were pushed and only one person is working on a specific branch (with `push -f`).  

Also, if I am working for the same functionality throughout several commits, I usually write the same commit message for each of them. Later on, after I am completely done with this functionality, I squash all commits that have the same message into one commit.  

In case one wants to retain the history of his work, then this format can be applied: 
- the same message for each commit, comma and then version. 
For example: 

commit # 1: `Fix xyz functionality` 

commit # 2: `Fix xyz functionality, v2` 

commit # 3: `Fix xyz functionality, v3`  

### How to squash 3 previous commits: 
1. `$ git rebase -i HEAD~3` 
2. from instructions apply `f` to commit # 2 and # 3  

_If you have un-committed code that prevents to run the command `git rebase -i HEAD~[N]`, do not forget to `git stash push` your code and then `git stash pop` after you are done._  

---------------------  

The difference between:  

- `$ git rebase [base_branch_name]` 
- `$ git rebase -i HEAD~[N]`  

**1st option**: rebases local branch to the base branch, i.e. applies all new commits of the base branch to the local branch at the beginning then applies all new commits of the local branch. Helpful and useful instead of merging. e.g. `$ git rebase origin/master`.  

**2nd option**: helps to squash, reword, or reorganize the local commits. Note, `N` should be only the number of your own commits. Do not change somebody else's commits.  

-----------------------------------  

### Change commit message on any N last messages: 
``` 
git rebase -i HEAD~[N] 
``` 
Replace `pick` with `reword` or `r` before each commit message you want to change.  

### Change last commit message 
``` 
git commit --amend 
```  

#### Remove all commits up until commit with revision number sha1Hash (commit id): 
``` 
$ git reset [sha1Hash] 
```

#### Remove all commits up until commit with revision number `sha1Hash` and delete all changes that were introduced after it: 
``` 
$ git reset --hard [sha1Hash] 
```  

#### Apply just an individual commit on a specific branch: 
``` 
$ git cherry-pick [sha1Hash] 
```  

----------- 

### How do I squash two non-consecutive commits? 
``` 
git rebase -i HEAD~[N] 
``` 
where N is a number of commits to squash. 
- Answer on [stackoverflow](https://stackoverflow.com/questions/3921708/how-do-i-squash-two-non-consecutive-commits).  

### Help with Vim to copy-paste (helpful for the case above): 
- Position the cursor where you want to begin cutting. 
- Press uppercase `V` to select whole lines (`v` to select characters). 
- Move the cursor to the end of what you want to cut. 
- Press `d` to cut (or `y` to copy). 
- Move to where you would like to paste. 
- Press `P` to paste **before** the cursor (`p` to paste **after**). 

-----------  
With squash and merge you can combine all your merge request’s commits into one and retain a clean history.


The difference between: 
- `merge --squash` 
- `git rebase -i HEAD~[N]`  

**1st option** will lose code iterations e.g. if our changes between `a` and `b` and `b` and `c` are squashed together so just looks like `a` to `c` in the commit history, the easiest. 
Another case: Say your bug fix branch is called bugfix and you want to merge it into master:
```
    git checkout master
    git merge --squash bugfix
    git commit // or: git commit -m 'Your custom commit message'
```             
    
This will take all the commits from the bugfix branch, squash them into 1 commit, and merge it with your master branch. 
(Omitting the -m parameter in `git commit` lets you modify a draft commit message containing every message from your squashed commits before finalizing your commit.)

Also, Github has the ability to perform squash merges, depending on the merge options enabled for the repository.
If squash merges are enabled, the "Squash and merge" option should appear in the dropdown under the "Merge" button.

**2nd option** is cleaner. Sometimes, it make sense do not squash everything into single commit, e.g.:
 - I work on a feature that has several logic points on the same PR, what if one of them I would like to cherry pick to another feature branch.   
 - It's nicer for tracking the history and following logical points.

---------------  

Advantages of the proposal
----------------------------  
 
 - Having smaller number of commits is better for merging. For example, when a big (very big) feature branch (which is better to evoid in general) is needed to be merged to master, it would be easier to do so when on both branches, we have fewer number of commits.  
 - having smaller number of commits on the base branch makes `git rebase [base_branch-name]` easier, no need to do `git rebase --continue` too many times  
 - `git cherry-pick ...` is easier  
 - git commits history is cleaner, less noisy from un-informational commits, which allows easier navigation back in history, comparison between commits and/or branches, this is very helpful for developers to learn code using proper commit messages