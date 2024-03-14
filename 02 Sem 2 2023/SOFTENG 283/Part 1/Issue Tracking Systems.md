Potentially a small component on this in assessment

### Overview
A platform for software developers to communicate and organise development activities. 

Functions like a discussion form
- The one who raises the issue will describe it in detail
- other developers will come to discuss it

Platform features
- Software component, assignees, schedules, status, resolution, tags
- Customisable as per context
- Change status as progress is made
### Software Bugs
Debugging is a core part of daily software development. Most developers say that debugging takes up 20-40% of their time

#### Bugs
- Bugs are responsible for massive costs both in lost revenue and developer time 
- Better issue tracking can save time and help improve software quality
- As software projects grow, efficient discovery and resolution of bugs becomes crucial to the products growth and stability



### Types of Issues
#### Bug Reports
- About a bug in the software
- Raised by a user / developer
- Should include:
	- Steps to reproduce
	- Expected behaviour
	- Actual behaviour
	- Stack trace or error message

#### Feature requests
- Add a new feature, e.g. Youtube display the dislike count
- Raised by user or the developer

#### Patches
- A **Planned** fix to the software
- Released at regular, pre-set intervals
- usually developed as part of the team's usual dev pipeline
- Should include
	- Version to patch
	- Path code - a code diff (buggy, correct)

>[!info] Issue life cycle
>![[Pasted image 20230728102713.png | center | 500]]

### GitHub Issues
A good way of doing issue tracking
Issues can have tags, assignees etc
`fixes #id` closes issue when in pr desc

Built into the [[Version Control Systems (VCS)#GitHub Distributed VCS |VCS]] server. 