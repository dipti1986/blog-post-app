# Blog Post App

### Resources:
* Jira: https://dip123.atlassian.net/jira/software/projects/DIP/boards/1
* Website: http://docker-swarm-manager.uksouth.cloudapp.azure.com/
* Video:  

## Contents
* [Brief](#brief)
   * [Additional Requirements](#additional-requirements)
   * [My Approach](#my-approach)
* [Architecture](#architecture)
   * [Database Structure](#database-structure)
   * [Github Branching Strategy](#github-branching-strategy)
   * [CI/CD Pipeline](#ci-pipeline)
* [Project Tracking](#project-tracking)
* [Risk Assessment](#risk-assessment)
* [Testing](#testing)
* [Front-End Design](#front-end-design)
* [Known Issues](#known-issues)
* [Future Improvements](#future-improvements)
* [Authors](#authors)

## Brief
The brief provided to us for this project sets the following out as its overall objective:
"To create a CRUD application with utilisation of supporting tools, methodologies and technologies that encapsulate all core modules covered during training."

In other words, I have been tasked with creating an application that utilises create, read, update and delete functions in order to function, such that I can demonstrate that I've learnt something over the last few weeks.

### Additional Requirements
In addition to what has been set out in the brief, I am also required to include the following:
* A Jira Board
* A relational database, consisting of at least two tables that model a relationship
* Clear documentation of the design phase, app architecture and risk assessment
* A python-based functional application that follows best practices and design principles
* Test suites for the application, which will include automated tests for validation of the application
* A front-end website, created using Flask
* Code integrated into a Version Control System which will be built through a CI server and deployed to a cloud-based virtual machine

### My Approach
To achieve this, I have decided to produce a simple blog post app that must allow the user to do the following:
* Create a user account (satisfies creat) that stores:
   * *User Name*
   * *Password*
* Create blog posts  with the following information:
   * *Title* of the post
   * *Body* of the post
* Edit and update their post details (satisfies 'Read' and 'Update')
* Delete their post (satisfies 'Delete')
* Read post they and other users have created (satisfies 'Read')

## Architecture
### Database Structure
Pictured below is an entity relationship diagram (ERD) showing the structure of the database. 

<img width="600" alt="Screenshot 2022-03-10 at 12 35 26" src="https://user-images.githubusercontent.com/84717522/157663028-5605c9a8-f668-40aa-9524-ff8d380c1673.png">

As shown in the ERD, the app models a one-to-many relationship between User entities and Post entities. This allows the user to create multiple posts.

### Github Beanching strategy
Instead of a single main branch, this workflow uses two branches to record the history of the project. The main branch stores the official release history, and the develop branch serves as an integration branch for features.

Each new feature should reside in its own feature branch.But, instead of branching off of main, feature branches use develop as their parent branch. When a feature is complete, it gets merged back into develop. Features should never interact directly with main.

<img width="600" alt="git-branching" src="https://user-images.githubusercontent.com/84717522/157659815-9f453983-ec4c-4081-b4ff-d6d1b80c74e7.png">

### CI/CD Pipeline
![ci-cd](https://user-images.githubusercontent.com/84717522/157670275-d8c4dbe8-4ecc-4ac8-8558-4dc5748a8e78.png)

Pictured above is the continuous integration contineus deployment pipeline process. 
* Developer creates new feature beanch
* Perform code change
* Push code change to Github
* Automated jenkins build triggered

Build stage for Feature branch
* 'Checkout SCM' (pull code from Github respository)
* 'Build Docker Image' (Build docker image with latest code)
* 'Test App' (Run unit test and code coverage in docker)

![Feature Branch](https://user-images.githubusercontent.com/84717522/157683444-ff0ed252-6d8e-409a-b191-9218b131e329.png)

Build stage for Develop branch
* 'Checkout SCM' (pull code from Github respository)
* 'Build Docker Image' (Build docker image with latest code)
* 'Test App' (Run unit test and code coverage in docker)
* 'Docker Push Registry' (Push docker image to docker registry)
* 'Deploy to Dev Environment' (Pull new tag docker image from docker registry and deploy to docker swarm)

![Develop Branch](https://user-images.githubusercontent.com/84717522/157683648-a79328df-0684-49c3-a31f-319e20edf817.png)

Build stage for master branch
* 'Checkout SCM' (pull code from Github respository)
* 'Build Docker Image' (Build docker image with latest code)
* 'Test App' (Run unit test and code coverage in docker)
* 'Create Release Tag and Push' (Push docker image to docker registry)
* 'Deploy to PRD Environment' (Pull new tag docker image from docker registry and deploy to docker swarm)

![Master Branch](https://user-images.githubusercontent.com/84717522/157684125-898305aa-df09-4e57-9793-0bc0bcf508ea.png)

Once the app is considered stable, it is then pushed to a separate VM for deployment. This service is run using the Python-based HTTP web server Gunicorn, which is designed around the concept of 'workers' who split the CPU resources of the VM equally. When users connect to the server, a worker is assigned to that connection with their dedicated resources, allowing the server to run faster for each user.

## Project Tracking
Jira was used to track the progress of the project (pictured below). You can find the link to this board here: https://dip123.atlassian.net/jira/software/projects/DIP/boards/1

![Jira](https://user-images.githubusercontent.com/84717522/157686861-57628e98-a2e4-4982-b754-5cdf8b4e6cca.png)

The board has been designed such that elements of the project move from left to right from their point of conception to being finished and fully implemented. From left to right, these lists are:
* *To Do*
   A list of Story/Task/Bug in the Sprint.
* *In Analysis*
   The story is analyse in this stage. Gather all required details for the story.
* *DOR*
   Once the story has all details and redy for implementation.
* *In Progress*
   Once the element has had any code written for it/exists in any way, it is placed in the 'in progress' stage.
* *In Testing*
   Once the element has been created, it moves to the 'testing' stage, where its functionality is tested.
* *In Review*
   Once the element tested, it moves to the 'review' stage, where code change is reviewed.   
* *Done*
   Any element that is considered to be finished (i.e. works according to its specification) lives in this stage.

## Risk Assessment
The risk assessment for this project can be found in full here: https://docs.google.com/spreadsheets/d/1WfKQAjsBfErpQOywRmnZbCe6zw7yFxESFB8WhQd69Es/edit?usp=sharing

Here's a quick screenshot:

![RiskAssessment][riskassessment]

## Testing
pytest is used to run unit tests on the app. These are designed to assert that if a certain function is run, the output should be a known value. Jenkins produces console outputs (pictured below) that will inform the developer how many tests the code passed and which tests they failed.

![pytest](https://user-images.githubusercontent.com/84717522/157693732-f297c764-c14f-4772-9e31-7d28b59ecfd4.png)

pytest also produces a coverage report to show how much of the code in the app has been successfully tested. Jenkins automatically moves this report to the 'templates' folder so that it can be navigated to in a browser, as shown in the picture below.

![pytest coverage](https://user-images.githubusercontent.com/84717522/157693314-8d8e61fd-307d-4c73-ad06-1e2f6a71cb6a.png)

## Front-End Design
The front-end of the app is rudimentary at this stage, as the front-end is built purely with very simple HTML. It is largely functional and stable, however.

When the user navigates to the URL, they are directed to the home page:

Home Page
<img width="1182" alt="Screenshot 2022-03-10 at 15 27 46" src="https://user-images.githubusercontent.com/84717522/157696308-936e9eb6-651e-49d5-9994-8c1b8dc72715.png">

Registration Page
<img width="1057" alt="Screenshot 2022-03-10 at 15 29 02" src="https://user-images.githubusercontent.com/84717522/157696303-d59a3019-b704-407d-9608-aba98447dabe.png">

Login Page
<img width="1067" alt="Screenshot 2022-03-10 at 15 30 00" src="https://user-images.githubusercontent.com/84717522/157696300-b524d94c-815b-4313-a84d-29cde41e239e.png">

User Home Page
<img width="1030" alt="Screenshot 2022-03-10 at 15 30 24" src="https://user-images.githubusercontent.com/84717522/157696298-8eff3e2a-2f65-40a5-9259-e1553950f863.png">

Create New Post Page
<img width="1023" alt="Screenshot 2022-03-10 at 15 31 23" src="https://user-images.githubusercontent.com/84717522/157696294-a6403f82-7ce4-441b-8f9b-418cb933bdd3.png">

Home Page with Post
<img width="1014" alt="Screenshot 2022-03-10 at 15 32 36" src="https://user-images.githubusercontent.com/84717522/157696292-c9b4fca5-3250-4d17-b36a-d0ef65637a05.png">

Edit Post Page
<img width="1006" alt="Screenshot 2022-03-10 at 15 32 59" src="https://user-images.githubusercontent.com/84717522/157696286-d0496e65-391d-42fb-9ad2-6ab60fcc0b5f.png">


## Known Issues
There are a few bugs with the current build of the app:
* Some time Nginx load balancer routing timeout

## Future Improvements
There are a number of improvements I would like to implement (outside of current bugs):
* Currently using SQLight. Extenal DB can be implemented.
* User interface can be improved.
* Can be added more user details.
* More option can be added in Post.

## Authors
Dipti Choudhary

[riskassessment]: https://i.imgur.com/btY8HRY.png

