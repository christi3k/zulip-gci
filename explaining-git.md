# Understanding Git

## What is in a Git repository?

Every Git repository has a **`.git`** and and **working copy**:

```
*--------------*
| .git         | <--- .git directory
|--------------|
| LICENSE      | <--- "working copy" or "working tree"
| README.md    |      (this is where you make changes to files as you work)
| Vagrantfile  |
| ...etc       |
*--------------*
```

The `.git` directory contains:

* The **refs database** for the repository, which contains its complete history. You can think of it like a *database* of all the work ever done on the project.
* The **index**, which is a **staging area** for the changes from your *working tree* that will become part of your next commit.

```
*------------------*
| .git             |
|  | refs database | <--- complete history; all changes ever made to the repository
|  | index         | <--- staging area for changing to your *working tree*
*------------------*      that will be included next time you `git commit`
```

Making changes to the `.git` directory is an advanced topic. Don't make changes to it or try to delete it if you are new to using Git.

## What happens when you `git clone`?

When you `git clone <url>` you create a **complete** local copy of the Git repository at *url*.

This copy has a **.git** directory and a **working copy**, just like every Git repository.

The Git repository that you have cloned to your laptop, or on a remote server, are basically the same as the repository that is hosted on GitHub:

```
 LAPTOP              GITHUB              REMOTE SERVER
*--------------*    *--------------*    *--------------*
| .git         |    | .git         |    | .git         |
|--------------|    |--------------|    |--------------|
| LICENSE      |    | LICENSE      |    | LICENSE      |
| README.md    | == | README.md    | == | README.md    |
| Vagrantfile  |    | Vagrantfile  |    | Vagrantfile  |
| ...etc       |    | ...etc       |    | ...etc       |
*--------------*    *--------------*    *--------------*
```

After you `git clone` from a GitHub url, you now have a complete copy of that repository on your laptop:

```
 LAPTOP                               GITHUB         
*--------------*                     *--------------*
| .git         |                     | .git         |
|--------------|    git clone        |--------------|
| LICENSE      |  <----------------  | LICENSE      |
| README.md    |    copies entire    | README.md    |
| Vagrantfile  |    repository       | Vagrantfile  |
| ...etc       |                     | ...etc       |
*--------------*                     *--------------*
```

At the moment you `git clone`, your `.git` directory will have the same **refs database** and **index** as the repository on GitHub. Both repositories are completely *in sync* when you first clone.

## What happens when you stage and commit changes

As you work, you'll make changes in your **working tree** or **working copy** on your laptop. (Remember, that's all of files not part of `.git` directory).

As you work, you modify files in the working tree and it becomes out of sync with the index. When you run `git status` git compares your *working tree* against your *index* and tells you what has changed.

In this example, we have created a new file, `changes.py`:

```                                      
 WORKING TREE                              INDEX                 
*-------------------------*               *--------------*   
|            LICENSE      |               | LICENSE      |   
|            README.md    |      !=       | README.md    |   
|            Vagrantfile  |  out of sync  | Vagrantfile  |   
| modified:  changes.py   |               | ...etc       |   
|            ...etc       |               |              |   
*-------------------------*               *--------------*   
```                                      

As you complete work, you **stage** these files to the **index** with `git add`
and this brings the working tree and the index back into sync:

```
 WORKING TREE                               INDEX                 
*-------------------------*                *-----------------------*   
|            LICENSE      |  git add       |          LICENSE      |   
|            README.md    |  ---------->   |          README.md    |   
|            Vagrantfile  |  adds changes  |          Vagrantfile  |   
| modified:  changes.py   |  to index      | staged:  changes.py   |   
|            ...etc       |                |          ...etc       |   
*-------------------------*                *-----------------------*   
```

Staging changes to the **index** is an interim step. At this point, the
**working tree** and the **index** are in sync, but the **refs databse** is
not.

```                                      
 INDEX                                     REFS DATABASE
*-----------------------*               *--------------*   
|          LICENSE      |               | LICENSE      |   
|          README.md    |      !=       | README.md    |   
|          Vagrantfile  |  out of sync  | Vagrantfile  |   
| staged:  changes.py   |               | ...etc       |   
|          ...etc       |               |              |   
*-----------------------*               *--------------*   
```                                      

You can keep working, adding files to the **index** as you go. When you have
complete your work, and staged all your changes, you **commit** them to the
**refs database** with `git commit`:

```
 INDEX                                        REFS DATABASE
*-----------------------*                    *--------------------------*   
|          LICENSE      |  git commit        |             LICENSE      |   
|          README.md    |  ---------->       |             README.md    |   
|          Vagrantfile  |  creates a commit  |             Vagrantfile  |   
| staged:  changes.py   |  and adds it to    | committed:  changes.py   |   
|          ...etc       |  refs database     |             ...etc       |   
*-----------------------*                    *--------------------------*   
```

Afer you commit, the working tree, index, and refs database are all in sync and `git status` reports `nothing to commit, working tree clean`:

```
 WORKING TREE               INDEX                       REFS DATABASE    
*--------------*           *--------------*            *--------------*   
| LICENSE      |           | LICENSE      |            | LICENSE      |   
| README.md    |    ==     | README.md    |     ==     | README.md    |   
| Vagrantfile  |  in sync  | Vagrantfile  |   in sync  | Vagrantfile  |   
| changes.py   |    ==     | changes.py   |     ==     | changes.py   |   
| ...etc       |           | ...etc       |            | ...etc       |   
*--------------*           *--------------*            *--------------*   
```

## How to keep repositories in sync

As you work and commit changes to the copy of your repository that you cloned
to your laptop, it will become out of sync with the copy of the repository that
is on GitHub:

```
 LAPTOP                            GITHUB          
*-----------------*               *--------------* 
| .git (updated)  |               | .git         | 
|-----------------|               |--------------| 
| LICENSE         |      !=       | LICENSE      | 
| README.md       |  out of sync  | README.md    | 
| Vagrantfile     |               | Vagrantfile  | 
| changes.py      |               | ...etc       | 
| ...etc          |               |              | 
*-----------------*               *--------------* 
```

To get the repository on your laptop and the repository on GitHub back in sync,
you need to *push* your changes to GitHub:

```
 LAPTOP                              GITHUB          
*-----------------*                 *----------------* 
| .git (updated)  |                 | .git (updated) | 
|-----------------|  git push       |----------------| 
| LICENSE         |  ------------>  | LICENSE        | 
| README.md       |  send new refs  | README.md      | 
| Vagrantfile     |  from laptop    | Vagrantfile    | 
| changes.py      |  to GitHub      | changes.py     | 
| ...etc          |                 | ...etc         | 
*-----------------*                 *----------------* 
```

### Fetching

Now you know how to push changes to GitHub. But what if there are changes that
you need to get from GitHub to your local copy of the repository?

You might need to do this to update a remote server, or to get changes someone
else has pushed to GitHub. The process is the same for both situations.

We'll use the example of a remote server. After you push changes from your
laptop to GitHub, those two are in sync. However, the remote server is not:

```
 LAPTOP                       GITHUB                      REMOTE SERVER
*----------------*          *----------------*          *--------------*
| .git (updated) |          | .git (updated) |          | .git         |
|----------------|          |----------------|          |--------------|
| LICENSE        |    ==    | LICENSE        |    !=    | LICENSE      |
| README.md      |    in    | README.md      |  out of  | README.md    |
| Vagrantfile    |   sync   | Vagrantfile    |   sync   | Vagrantfile  |
| changes.py     |          | changes.py     |          | ...etc       |
| ...etc         |          | ...etc         |          |              |
*----------------*          *----------------*          *--------------*
```

To get the remote server in sync, you first need to update the **refs db** on
the remote server by **fetching** new commits:

```
 REMOTE SERVER                        GITHUB            
*-----------------*                  *-----------------*
| .git (updated)  |                  | .git (updated)  |
|-----------------|    git fetch     |-----------------|
| LICENSE         |  <-------------  | LICENSE         |
| README.md       |    get new refs  | README.md       |
| Vagrantfile     |    from GitHub   | Vagrantfile     |
| ...etc          |                  | changes.py      |
|                 |                  | ...etc          |
*-----------------*                  *-----------------*
```

Notice that `git fetch` updated the refs db in the `.git` directory, but it did
not change the working directory. This is expected because `git fetch` updates
only the refs database and not your working tree:

```
 WORKING TREE            REFS DATABASE
*-------------*        *--------------------------*   
| LICENSE     |        |              LICENSE     |   
| README.md   |   !=   |              README.md   |   
| Vagrantfile | out of |              Vagrantfile |   
| ...etc      |  sync  | new commit:  changes.py  |   
|             |        |              ...etc      |   
*-------------*        *--------------------------*   
```                   

To bring the changes from these new refs into your working directory, you need
to `git merge` or `git rebase`. 

For Zulip, you'll always want to use `git
rebase`:

```
 WORKING TREE                                 REFS DATABASE
*-------------------------*                  *--------------------------*   
|             LICENSE     |    git rebase    |              LICENSE     |   
|             README.md   |  <-------------  |              README.md   |   
|             Vagrantfile |    apply new     |              Vagrantfile |   
| new commit: changes.py  |    commits       | new commit:  changes.py  |   
|             ...etc      |    from refs db  |              ...etc      |   
*-------------------------*                  *--------------------------*   
```                   

