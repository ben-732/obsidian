### Benefits of VCS
- Collaboration
	- Error free and smooth collaboration among team members
- Storing Versions
	- Secure, integral and hassle-free storage of you file or project versions after making changes
- Restoring previous versions
	- Ability to always revert to the older versions if needed
- Understanding what happened
	- Changes can always be accompanied by comments explaining the reason for change
- Backup
	- Copies of the project along with the previous versions are instantly available


### Types of VCS
#### Local VCS
- On a local computer
- Not good for collaboration
- Error Prone 
	- Its easy to forget which directory you're in and accidentally write to the wrong file or copy over files you don't mean to
- Example: RCS

#### Centralized VCS
- Traditional way developer tried to do version control
- Single server contains all the versioned files and a number of clients that check out files form that central place
- Risk - Single point of failure that the server represents
- Example: SVN, CVS, Perforce

#### Distributed VCS
- Everybody gets a full copy of the files
- many backups
- Several types of workflows that aren't possible in a centralised system
- Examples: Git, Mercurial, Bazaar

### Git VCS
- Git is a free and open distributed version control system
- Primarily used for **source code management in software development** but can but used to keep track of changes in any set of files. 
- Was created by **Linus Torvalds** in 2005 for development of the Linux kernel. 
- It is freely distributed under the terms of the GNU General Public license version 2. 

#### Core Terms
**Commit:** Record of changes
**Branch:** 
- Chronological chain of commits
- Master: the main/default branch
- Independent line of development
- Avoid merging unstable changes into the main code, you can clean up changes before you merge them to the master branch. 

#### Basic Commands
- `git init` Create an empty repository or reinitialize an existing one
- `git add` add file contents to the index (staging area)
- `git commit` record changes to the repositiory
- `git checkout` switch among files, commits, or branches
- `git push` update remote repository with the committed files
- `git fetch` download files from remote repository
- `git merge` join two or more development histories together
- `git pull` fetch from remote repository and merge with another repo or local branch
- `git branch` create, list, rename, or delete a branch
- `git status` show the current status of branching and HEAD pos



### GitHub Distributed VCS 
A VCS Repository hosting service
- A home for `.git` repositories
- Provides a graphical interface for exploring git repos
- Handles the server aspects of git
- Enable *social coding* and open learning
- Provides issue and bug tracking, project analytics, user access control, and more
- Boosts team collaboration through additional features. 

>[!exercise]-
>![[Pasted image 20230728104427.png | center | 500]]
>![[Pasted image 20230728104901.png | center | 400]]

#### Learning Git Branches
Amazing Website https://learngitbranching.js.org

### Delta Encoding
- Storing every version of a file takes up lots of space
- Idea: just store the differences between versions
- Differences (aka. Deltas, Diffs or Patches) can be calculated automatically with various diff algorithms. 
- These can then be recorded in a different file and used to update files ("patch software")

### Unified Diff Format
- The **Standard** format for exchanging diffs, understood by most VCS
- Line oriented, only full line insertions and deletions. 
- No line parts or moving
- @@1, 5@@ refers to old start line, number of lines in chunk
```diff
Index: X.java
=============
--- X.java (revision 6094)
+++ X. java (working copy)
@@ -1,5 +1,5 @0 old
class X {
- // todo
  void m() {
+   return;
  }
}
``` 


### VCS Centralised
- Developers work on their local copies
- Developers synchronise their local copies with the central repository
- Repositories usually use delta encoding for the versions (automatic with git)
- Two ways to avoid conflicts: locking and merging


#### Product Space
What is versioned? How is the data organised?

- Just files, each file has a version number which is increased when the file is changed (e.g. CVCS)
- Files and folders: the whole file-folder structure has a single version number which in incremented for any change that is done to any file / folder (e.g. SVN, GIT)
- Other Data models: e.g. data models of a database

#### Version Space
How is it versioned? How are versions organised?

- Version Identifiers: serial numbers, timestamps, or hashcodes. 
- Version history: 
	- How are versions ordered: Parent-version / Child version
	- Versions with several parents - Merging
	- Versions with several children - Branching

#### Branches
Different copies of a project which are developed simultaneously. 
> "self-maintained lines of development"
- ﻿﻿One **main** branch (trunk/master/default)
- ﻿﻿**Maintenance branches**: used for maintaining old versions which are still widely used (e.g. commercial OS)
- ﻿﻿**Experimental branches**: used for trying out new features before merging them into the trunk
- ﻿﻿**Release branches**: for impending release - only bug fixes may be committed (usually enforced by a release manager

#### Tags
Particular marked versions of the project
- ﻿﻿can be used to refer to and recreate an old version
- ﻿﻿actually also like a copy of the project at a particular point in time
- ﻿﻿difference to branches: usually not changed any more

#### Concurrency
Concurrent file access, The "Lost Update" problem
- ﻿When sharing files, developers can accidentally overwrite each others' changes
- ﻿﻿Consider two developers working on the same file
- ﻿﻿Two approaches for solving this:
	- ﻿﻿Reserved checkouts ("locking")
	- ﻿﻿Unreserved checkouts ("merging")
- ﻿﻿Many old version control systems support only locking (e.g. RCS, SCCS)
- ﻿﻿Newer systems offer merging
- ﻿﻿Both approaches have disadvantage

##### Locking (Reserved Checkouts)
- Traditional way of concurrent development
- Only one person can edit a file at a time
- Before getting write access, developer has to acquire the lock of the file. 
- Attempts to get the lock while someone else has it will fail. 

##### Merging (Unreserved Checkouts)
- Everybody can modify their working copy whenever they want
- Changes have to be merged back into the DB before they can be written to the repository

#### Merge Conflicts
Both textual and semantic conflicts need to be resolved by the user. 

##### Textual Conflicts
- Changes of different developers are overlapping (Or very close to each other)
- Merge tool cannot automatically merge them
- Merge tool detects such conflicts and reports them to the user
- VCS will refuse to commit a file with unresolved textual conflicts to the repository. 

##### Semantic Conflicts
- Changes are semantically incompatible, but not overlapping. 
- Example: Developer A changes method signature of method `m`. Developer B inserts method calls to `m` using the old signature. 
- Non-overlapping semantic conflicts are not detected by a generic merge algorithm
- Can be avoided by following specifications and communicating with other developers

#### Locking vs Merging
##### Pro Merging
1. ﻿﻿﻿Administrative problems: people forget releasing their locks; frequently administrators have to do it
2. ﻿﻿﻿Unnecessary serialisation: very counter-productive
	- ﻿﻿Locking prevents people from editing different parts of the same file
	- ﻿﻿In reality conflicts occur rarely in such cases, and can be resolved without problems
	- ﻿﻿Conflicts usually indicate lack of communication
		- ﻿﻿Developers have not agreed on a proper design
		- ﻿﻿With mutual agreement on design conflicts, changes are usually straightforward to merge
3. False sense of security: locking does not prevent semantic conflicts between file

##### Pro Locking
1. Un-mergeable files: A generic merging tool does not work for all file types, 
	- For some file formats (Eg. java class files) merging can lead to many conflicts
	- Conflicts can be very hard to resolve (e.g. for binary formats)
	- One of two conflicting changes can get lost (because they cannot be merged)
2. Tradition: an organisation might have always used a locking VCS

### Best Practices
- Complete one change at a time and commit it. 
	- If you are commiting several changes together, you cannot undo/redo them indivually
		- Sometimes individual changes are needed. 
		- Sometimes individual changes are excluded. 
	- Continuous integration (see also XP practice)
		- ﻿﻿If you make several changes conflicts are much more likely
		- ﻿﻿Merging simple changes is much easier
	- Data safety
		- If something happens to your local machine
		- Your repo is your backup
		- Even if the repo is destroyed other developers will probably have their own local copy
- Don't break the build
	- Only commit changes that preserve system integrity
	- No changes that make compilation or tests fails
	- Test Driven Development (XP Practices)
		- Write tests for every change
		- Run tests before committing (At least some of them)
- Only commit source files
	- ﻿Files that are actually necessary for your software (including documentation)
		- ﻿﻿Not generated files (e.g. . class, .exe)
		- ﻿﻿Not temporary files (e.g. irrelevant data or log files)
		- ﻿﻿Source files are often textual and generated files binary
	- Unnecessary files waste space (other people need to download them when checking out / updating)
	- Most binary files are unmergeable (easily leads to conflicts that can't be resolved manually)
- Use the log
	- Write log entries for each change
	- What has been changed and why
		- Like a short blog post
		- Communicating with other devs
	- Makes it easier to find changes (both good and bad ones)
- Communicate with other developers
	- Before changing existing code
		- See who else is working on it 
		- Ask that person about your change before committing
	- Before starting something new