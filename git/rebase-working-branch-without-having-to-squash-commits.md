# Rebase working branch without having to squash commits

Full credit to: https://blog.oddbit.com/post/2019-06-17-avoid-rebase-hell-squashing-wi/

1. Check out a new branch based on `master` (or the appropriate base branch if your feature branch isn’t based
   on `master`):
   ```bash
    git checkout master
    git checkout -b work
    ```
2. Bring in the changes from your messy pull request using git merge --squash:

    ```bash
    git merge --squash my_feature
   ```
   This brings in all the changes from your `my_feature` branch and stages them, but does not create any commits.
3. Commit the changes with an appropriate commit message:
    ```bash
    git commit
    ```
   At this point, your work branch should be identical to the original `my_feature` branch (running git diff
   `my_feature_branch` should not show any changes), but it will have only a single commit after `master`.
4. Return to your feature branch and reset it to the squashed version:
    ```bash
    git checkout my_feature git reset --hard work
    ```
5. Update your pull request:
    ```bash
    git push -f
    ```
6. Optionally clean up your work branch:
    ```bash
    git branch -D work
    ```

