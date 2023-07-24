Lab: Finding the bug in your project
====================================

The slowest, most tedious way of finding a bad git commit is something
we\'ve all done before. You checkout some old commit, make sure the
broken code isn\'t there, then checkout a slightly newer commit, check
again, and repeat over and over until you find the flawed commit.

Using `git bisect` is a much
better way. It\'s like a little wizard that walks you through recent
commits, asks you if they are *good* or *bad*, and narrows down the
broken commit. In this blog post, I encourage you to create a fresh git
repository and walk through each step. Hopefully, you\'ll gain an
intrinsic understanding of `git bisect` by the end of the exercise.

### Setting up your lab environment

1.  #### Create an experimental git repository.

    Copy and paste the following lines:
 
    ```
    mkdir git_bisect_tests
    cd git_bisect_tests
    git init
    ```
    

2.  #### Create a history of commits to play with.

    Copy and paste the following lines. This will create a commit
    history for a file called 'test.txt' that contains the opening
    lyrics to the childrens song "Row Row Row Your Boat".

    
    
    ```
    echo row > test.txt
    git add -A && git commit -m "Adding first row"
    echo row >> test.txt
    git add -A && git commit -m "Adding second row"
    echo row >> test.txt
    git add -A && git commit -m "Adding third row"
    echo your >> test.txt
    git add -A && git commit -m "Adding the word 'your'"
    echo boat >> test.txt
    git add -A && git commit -m "Adding the word 'boat'"
    echo gently >> test.txt
    git add -A && git commit -m "Adding the word 'gently'"
    sed -i -e 's/boat/car/g' test.txt 
    git add -A && git commit -m "Changing the word 'boat' to 'car'"
    echo down >> test.txt
    git add -A && git commit -m "Adding the word 'down'"
    echo the >> test.txt
    git add -A && git commit -m "Adding the word 'the'"
    echo stream >> test.txt
    git add -A && git commit -m "Adding the word 'stream'"
    ```
    
    Output:

    ![](./images/Screen%20Shot%202012-04-17%20at%2010.56.04%20AM.png)

3.  #### Find the bug in 'test.txt' file.

    Look at the contents of test.txt. There is a glaring error: the word
    'car' is where the word 'boat' should be. That happened during our
    commit history -- at some point, the commit of the word 'boat' was
    overwritten by the commit for the word 'car'.

    ![](./images/Screen%20Shot%202012-04-17%20at%2010.57.02%20AM.png)


### The Experiment

1.  #### Use git log to find a *good* commit, and a *bad* commit.

    First off, let's try to find a commit that still had the word
    'boat', and not the word 'car'. Check out your git log:

    ![](./images/Screen%20Shot%202012-04-17%20at%2010.58.50%20AM.png)

    We know that the newest commit in the log is bad, so we will
    classify this as our *bad* commit. For the sake of simplicity, let's
    say our *good* commit is the first commit we made for the word
    \'boat\':

    ![](./images/Screen%20Shot%202012-04-17%20at%2011.02.00%20AM.png)

2.  #### With a *good* commit, and a *bad* commit, we're ready for the process of elimination -- `git bisect`

    Git bisect is like a wizard: it guides you step by step through a
    process of elimination until you find the commit that broke your
    code. Once you type `git bisect start`, you have started the wizard and
    it won't end until you type `git bisect reset`.

3.  #### Start up the git bisect wizard

    ![](./images/Screen%20Shot%202012-04-17%20at%2011.14.19%20AM_0.png)

4.  #### Let the git bisect wizard know of a *good* commit.

    In this case, it was the 'Adding the word boat' commit we picked
    above.
    
    ![](./images/Screen%20Shot%202012-04-17%20at%2011.16.10%20AM.png)

5.  #### Let the git bisect wizard know of a *bad* commit.

    In this case, let's use the very last commit in the repository,
    since we know that had the word 'car' in
    it.
    
    ![](./images/Screen%20Shot%202012-04-17%20at%2011.18.34%20AM_0.png)

6.  #### Look at the contents of test.txt

    When we typed `git bisect bad` in this step, git bisect checked
    out an old commit for us -- the commit halfway between the latest
    *bad* commit and the known *good* commit. This is how bisect works
    -- it cuts the commit history down in halves until it finds the
    original bad commit.

    ![](./images/Screen%20Shot%202012-04-17%20at%2011.35.48%20AM_0.png)

7.  #### The word \'car\' is still there = BAD!

    This commit is still bad, so we tell bisect about how bad it is:
    `git bisect bad`

    ![](./images/Screen%20Shot%202012-04-17%20at%2011.37.23%20AM_0.png)

8.  #### Look at the contents of test.txt again.

    Once again, git bisect checked out a new commit halfway between the
    latest *bad* commit and the known *good* commit.

    ![](./images/Screen%20Shot%202012-04-17%20at%2011.39.14%20AM_0.png)

9.  #### The word \'boat\' is there = GOOD!

    Now we see the word 'boat', so this is a *good* commit. Let's let
    git bisect know how good it is: `git bisect good`
    
    ![](./images/Screen%20Shot%202012-04-17%20at%2011.41.01%20AM.png)

10. #### ALL DONE - The bad commit has been found!

    Plain as day, git reports back to you: "I found the first bad
    commit"

    You are now free to investigate the problem, notify the author,
    write patches, and take care of business.

11. #### To end your git bisect wizard, simply type git bisect reset

    ![](./images/Screen%20Shot%202012-04-17%20at%2011.43.36%20AM_0.png)

With `git bisect`, We can narrow down broken code within a few seconds on repositories
with a dozen developers adding several dozen commits per hour.