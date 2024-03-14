Quality in software is not only code quality, but is also about building the software "right"

![[Pasted image 20230811104927.png | center | 400]]

>[!info]- Requirements development process
>![[Pasted image 20230811105016.png]]

Only about 16% of projects are completed successfully, with 50% being challenged in some way. 

**The top factors in successful projects:**
- ﻿﻿User involvement
- ﻿﻿Executive management support
- ﻿﻿Clear Statement of Requirements
- ﻿﻿Proper planning
- ﻿﻿Realistic expectations

**Top indicators of challenged projects:**
- ﻿﻿Lack of user input
- ﻿﻿Incomplete Requirements &  Specifications
- ﻿﻿Changing Requirements &  Specifications
- ﻿﻿Lack of executive support
- ﻿﻿Technical incompetence

#### Importance of requirements
The further along you get in the development process, the more it costs to add or fix things in a project. 

>[!info]- Cost vs time
>![[Pasted image 20230811105347.png]]

Furthermore, in physical applications like a rocket launch or an autonomous vehicle. A lack of understanding in the requirements can lead to dire consequences. 



#### Types of Requirements
**Functional Requirements** - These are things the product must do
- The product shall make the user aware of the current time. 

**Non-Functional Requirements** - These are things the product must have, *quality attributes*
- The product shall produce the schedule within 3 seconds of the user's request

#### RE Challenges
>"Getting requirements right might be the single most important and difficult part of a software project."

Ambiguity: diverse interpretations of the same requirement

Changing requirements, different visions for the outcome, scope creep. 
### RE what vs how Tension
Requirements are not about the written requirements but about the problem that needs to be solved.   Ideally they should not describe the solution.

Ideally describe what the problem is without describing how to solve the problem. 

#### Examples:
- The product should show the current time
- The product should be able to lock a user out after repeated failed login attempts
	- The system shall prevent unauthorised access
- The system should process the order when the user submits it
	- The system shall process the order when the customer indicated it is complete
- The system should store user information in a database
	- The system should store user information
- The user should be able to view previous orders

#### Other Questions
**Who:** meets that requirement? is it for?
**Why:** 

#### Application Domain vs Machine Domain
Application Domain: The world in which the problem exists
Machine Domain: The world in which software solutions are developed


#### When do we solve the problem?
>[!info] Waterfall vs Agile methods of development
![[Pasted image 20230804103059.png | center| 600]]

#### Just in tiem RE
Define the requirements only when needed, and only at the detail required
Public issue trackers, Discussion forums

#### Balance
Global vs local RE. Trade off flexibility between strategic RE and just in time RE


### Requirements Elicitation
>﻿"Elicitation involves the actions that are taken to understand the users and discover their needs."

Elicitation demands iteration. The participants in an elicitation discussion won't think of everything up front, and their thinking will change as the project continues.
#### Challenges
- The customer does not always communicate their requirements well
- The customer does not always disclose quality criteria, because they are self-evident to them

#### Techniques
- ﻿﻿Interviews
	- ﻿﻿Structured
	- ﻿﻿Semi-Structured
	- ﻿﻿Unstructured
- ﻿﻿Questionnaires
- ﻿﻿Observations
- ﻿﻿Document analysis
- ﻿﻿Collaborative Sessions
	- ﻿﻿Brainstorming
	- ﻿﻿Focus groups
**Comparison of techniques**
![[Pasted image 20230815142157.png | center | 500]]

#### Interview / Focus Group 
##### Tips
- ﻿Be prepared
- ﻿﻿Listen actively
- ﻿﻿Paraphrase and repeat
- ﻿﻿Drill down
	- ﻿﻿Keep asking why

##### Steps
- ﻿﻿Prepare questions
- ﻿﻿Schedule interview
- ﻿﻿Conduct interview
- ﻿﻿Follow-up
	- ﻿﻿Clarification
	- ﻿﻿Thanks

##### Bias in questions
- ﻿﻿The way we ask questions can bias the response
- ﻿﻿About how fast were the cars going when they hit each other?

- ﻿﻿Would your answer about the car crash be different if I replaced hit with a different verb?
	- ﻿﻿Smashed?
	- ﻿﻿Collided?
	- ﻿﻿Bumped?
	- ﻿﻿Contacted

A study conducted in the US shows how different verbs can change peoples perception about the speed of the collision
![[Pasted image 20230815142958.png | center | 300]]

##### Good questions
- ﻿﻿Context-free: do not imply an answer
- ﻿﻿Open-ended: encourage more than a yes/no response

- ﻿﻿Do not lead the interviewee
- ﻿﻿Ask open-ended, context-free questions

**Examples of better questions:**
1. ﻿Do you think the user should have to enter a password?
	- Describe how the system should safeguard sensitive data.
2. ﻿﻿﻿Should the system automatically save the user's work every 15 minutes?
	- ﻿What would a user do if the system crashed while they were using it
3. ﻿﻿﻿Can one user see database entries saved by another user
	- ﻿Tell me about the accessibility of data saved by a user

>[!info]- More good questions to ask
>- What are some reasons you or your colleagues would use the new product?
>- ﻿﻿What goals might you have in mind that this product could help you accomplish?
>- ﻿﻿What problems do you expect this product to solve for you?
>- ﻿What external events are associated with the product?
>- ﻿What words would you use to describe the product?
>- ﻿Are there any constraints or rules to which the product must conform?
>- ﻿﻿How is the product similar (different) to the way you currently do business?
>
>- ﻿What aspects of the current product or business process do you want to retain (replace)?
>- ﻿Which aspects of the product are most critical to creating business value?
>- ﻿﻿What aspect of the product most excites you?
>- ﻿﻿What aspect of the product will be most (least) valuable to you?
>- ﻿What is most important to you about the product?
>- ﻿﻿How would you judge whether the product was a success!
>- ﻿﻿Can you describe the environment in which the product will be used

## Requirement Specification
- Specification Methods
- Story maps and acceptance criteria
- Writing good user stories

### Methods
The Traditional method is software requirements specification SRS e.g. `IEEE Standard 830-1998`

SRS is a very detailed, you need lots of information for it. They are formal documents that could represent a contract. 

>[!info]- SRS
>![[Pasted image 20230815215355.png]]
>![[Pasted image 20230815215405.png]]

##### Quality Characteristics of SRS
- Unambiguous
- Complete
- Verifiable
- Consistent
- Modifiable
- Traceable
- Ranked for importance
- Correct

#### User Stories
- Card 
	- A Thing that you can put on your document or into your management software
- Conversation
	- A user story is said to be the start of a conversation
- Confirmation

This is not a traditional requirement specification, Documents a user need. 

As a `role`, I want to `do something` so that some `rational`

>[!example] User story: Customer withdraws Cash
>As a customer, I want to withdraw cash from an ATM, so that I don't have to wait in line at the bank

##### Criteria of good user stories - INVEST
- **I**ndependent
	- There should not be overlap between different user stories
- **N**egotiable
	- They should be high level, flexible and not a contract
- **V**aluable (to user or customers)
	- If its not valuable, why are you doing this ?
- **E**stimable
	- Should be able to estimate the time it will take
- **S**mall
	- Should be small and easily achievable
- **T**estable
	- Should be able to test for outcomes

##### Common user stories problems
1. User Stories Conflate Problems with Solutions  
	- Avoid including implementation, so the development team can exercise creativity in their solution  design.
3. Misplaced Requirements (and missing rationale)  
	- The “so that...” section should describe the rationale, not additional requirements.
5. Ultra-huge story (The Odyssey)  
	- If there are a lot of “ands” or “ors” in your team’s user stories, consider breaking them down into  multiple stories, so they can be estimated and understood clearly.
	- Can it be implemented in one sprint?
1. Technical User Story  
	- Beware of user stories where “so that” is a technical capability, not any value to the end user.
2. Poorly defined user roles  
	- Coming up with the best set of roles for a project can be complicated, and often roles end up being too vague.
	- Roles can also be too specific, where two “different” users have duplicate stories.
1. Parakeet Value
	- Repeating the main requirement
	- The story’s “so that” phrase is a restatement of the story’s “I want” phrase


### Stories Maps 
This is an example of a user story sorted by priority and chronological order
![[Pasted image 20230815220104.png | center | 600]]

### Acceptance Criteria
Put specific constraints here

- Defines the boundaries of a user story
- Used to confirm when a  story is completed and working as intended
- Forms the tests that will confirm the system addresses the user needs

If your acceptance criteria cannot easily be turned into a software test, then they might not be good acceptance criteria.

#### Examples
- I must use my bank card to get money out of the ATM.  
- I can withdraw money as long as I ask for no more than is in my account.  
- I can withdraw no more than $400 at a time.  
- If I have more than one account, I can choose which account to take money from.  
- If there is a problem with my request, I am told about the problem and told how I can get help.  
- My card will be returned to me when I get my money.  
- My card will be retained by the ATM if it has been disabled.
#### Given when then
- Given `some condition(s)`  
- When `some action occurs`  
- Then `a set of observable consequences`

>[!example]- 
>**GIVEN** the account is in credit, the card is valid  
and the ATM contains cash  
>
**WHEN** the customer requests to withdrawal an  
amount less than the card’s limit
>
>**THEN** the account is debited and the cash is  
dispensed and the card is returned.  
>
>---
>**GIVEN** the account is overdrawn and the card is  
valid  
>
**WHEN** the customer requests to withdrawal  
>
**THEN** a rejection message is displayed and the  
account is not debited and cash is not dispensed  
and the card is returned.


