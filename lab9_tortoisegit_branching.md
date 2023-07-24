

### Lab: Create and switch branches using TortoiseGit

Git is very powerful at branching and tagging. It is very easy to create branches and tags.

**Creating a Branch or Tag**

Creating a branch is very simple: `TortoiseGit` → `Create Branch...`

![](./images/34.png)

`Branch`: input your branch name.

Creating a tag is very simple: `TortoiseGit` → `Create Tag...`

![](./images/35.png)

`Tag`: input your tag name.

You can choose one commit that base on.

- **HEAD**: Current commit checked out.
- **Branch**: The latest commit of chosen branch.
- **Tag**: The commit of chosen tag.
- **Commit**: Any commit, you click `...` to launch log dialog to choose commit. You also can input commit hash, or friendly commit name, such as HEAD~4.

If you want your working tree to be switched to the newly created branch automatically, use the `Switch to new branch/tag` checkbox. But if you do that, first make sure that your working tree does not contain modifications. If it does, those changes will be merged into the branch working tree when you switch.