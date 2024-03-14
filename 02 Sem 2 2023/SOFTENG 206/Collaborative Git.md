Building on [[Version Control Systems (VCS)#GitHub Distributed VCS | 283 Git VCS]] 

Useful for this course because it allows sharing of code between multiple people. It also allows a history to be kept between commits. 

Created in 2005 by Linus Torvalds for use on the linux kernel. 
- Fast 
- Able to handle large projects like the Linux Kernel efficiently
- Simple design
- strong support for non-linear development (branching)
- Fully distributed

## Github Flow
In this course we must use GitHub flow.
- Git branches
- Pull requests
- Code Reviews


### Branching Workflow
There are three main branching workflows
- Git flow - Common in industry
- GitHub Flow - Open source projects / this course
- Gitlab Flow

#### Git Flow
There are two main branches. Main and Develop

Main represents releases and only gets merged into when people are happy with commits. 

Features branches are created of develop and merged into develop. 

There are hot fix branches for when there is something wrong is main. This is then merged into both main and develop. 

Pros:
- There are some command line tools for git flow
- Ideal when there are multiple versions in production
Cons:
- Difficult to read commit history
- It is not recommended when you need to maintain aa single version in production. 

#### Github Flow
Only two types of branch, Main and feature. 

Commits are added to feature branch, they are discussed and reviewed until they are merged by an open pull request. 

Rules:
1. Anything in main is deployable (for 206 the main branch is the submission at the deadline)
2. To work on something new, create a branch off from main and give it a descriptive name (e.g. feature/login)
3. Commit to that branch locally and regularly, push your work to the same named branch on the server (so other team members can collaborate)
4. when you need feedback or help, or you thin the branch is ready for merging, open a pull request. 
5. After someone has opened a pull request,==oop==
6. ==something==

Pros:
- A simpler alternative to Git Flow
- It is ideal when it needs to maintain only a single version in production
- It works very well for individual or small teams 

Cons:
- Not recomended for multple versions in production