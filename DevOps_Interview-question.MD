# DevOps
------
## CI/CD process using devops Tools?

* Developers develop the code and this source code is managed by Version Control System tools like Git etc.
* Developers send this code to the Git repository and any changes made in the code is committed to this Repository.
* Jenkins pulls this code from the repository using the Git plugin and build it using tools like Ant or Maven.
* Configuration management tools like Ansible deploys & provisions testing environment and then Jenkins releases this code on the test environment on which testing is done using tools like selenium.
* Once the code is tested, Jenkins send it for deployment on the production server (even production server is provisioned & maintained by tools like Ansible).
* After deployment It is continuously monitored by tools like Nagios.
* Docker containers provides testing environment to test the build features.
------------------------------------------------------------------

## What are the advantages of DevOps?

Technical benefits:
Continuous software delivery
Less complex problems to fix
Faster resolution of problems
Business benefits:

Faster delivery of features
More stable operating environments
More time available to add value (rather than fix/maintain)

-------------------------------------------------------------
## Waterfall Model ? 
* Requirements gathering and analysis. This is the phase where you get the requirements from the client for developing an application.
* Design phase where you prepare a blueprint of the software. Here, you think about how the software is actually going to look like.
* Implementation phase where you begin with the coding for the application. The team of developers works together on various components of the application.
* Verification phase There are various tests conducted on the application such as unit testing, integration testing, performance testing, etc.
* Maintenance phase the application is monitored for performance. Any issues related to the performance of the application are resolved in this phase.

----------------------------------------------------------------------
## Agile Methodology ?
Agile Methodology is an iterative based software development approach where the software project is broken down into various iterations or sprints. Each iteration has phases like the waterfall model.
* a company releases the application with some high priority features in the first iteration.
* After its release, the end-users or the customers give you feedback about the performance of the application.
* Then you make the necessary changes into the application along with some new features and the application is again released which is the second iteration.
* You repeat this entire procedure until you achieve the desired software quality.

Advantages of Agile Model:
* It adaptively responds to requirement changes favorably
* Fixing errors early in the development process makes this process more cost-effective
* Improves the quality of the product and makes it highly error-free
* Allows for direct communication between people involved in software project
* Highly suitable for large & long-term projects
* Minimum resource requirements & very easy to manage
---------------------------------------------------------------------------

## What is agile development and Scrum ?
Agile development used as an alternative to Waterfall development practice. In Agile, the development process is more iterative and incremental, there is more testing and feedback at every stage of development as opposed to only the last stage in Waterfall.

Scrum is used to manage complex software and product development, using iterative and incremental practices. Scrum has three roles ie product owner, scrum master, and team.

-----------------------------------------------------------------------------

## How is DevOps different from agile methodology?

DevOps is a culture that allows the development and the operations team to work together. This results in continuous development, testing, integration, deployment, and monitoring of the software throughout the lifecycle.

Agile is a software development methodology that focuses on iterative, incremental, small, and rapid releases of software, along with customer feedback. It addresses gaps and conflicts between the customer and developers

---------------------------

## What are the different phases in DevOps?

Plan - Initially, there should be a plan for the type of application that needs to be developed. Getting a rough picture of the development process is always a good idea.

Code - The application is coded as per the end-user requirements. 

Build - Build the application by integrating various codes formed in the previous steps.

Test - This is the most crucial step of the application development. Test the application and rebuild, if necessary.

Integrate - Multiple codes from different programmers are integrated into one.

Deploy - Code is deployed into a cloud environment for further usage. It is ensured that any new changes do not affect the functioning of a high traffic website.

Operate - Operations are performed on the code if required.

Monitor - Application performance is monitored. Changes are made to meet the end-user requirements.

-------------------------------------

##  How will you approach a project that needs to implement DevOps?

Stage 1
An assessment of the existing process and implementation for about two to three weeks to identify areas of improvement so that the team can create a road map for the implementation.

Stage 2
Create a proof of concept (PoC). Once it is accepted and approved, the team can start on the actual implementation and roll-out of the project plan.

Stage 3
The project is now ready for implementing DevOps by using version control/integration/testing/deployment/delivery and monitoring followed step by step.
By following the proper steps for version control, integration, testing, deployment, delivery, and monitoring, the project is now ready for DevOps implementation. 

## The difference between Git and SVN version control systems ?
Git is a distributed version control system, whereas SVN is a centralized version control system. Git uses multiple repositories including a centralized repository and server, as well as some local repositories.


*Git is a distributed VCS; SVN is a non-distributed VCS.

*Git has a centralized server and repository; SVN does not have a centralized server or repository.

*The content in Git is stored as metadata; SVN stores files of content.

*Git branches are easier to work with than SVN branches.

*Git does not have the global revision number feature like SVN has.

*Git has better content protection than SVN.

*Git was developed for Linux kernel by Linus Torvalds; SVN was developed by CollabNet, Inc.

*Git is distributed under GNU, and its maintenance overseen by Junio Hamano; Apache Subversion, or SVN, is distributed under the open source license.

---
## Jenkins Pipeline?

#### Declarative Pipeline

```
pipeline {
    agent any 
    stages {
        stage('Build') { 
            steps {
                // 
            }
        }
        stage('Test') { 
            steps {
                // 
            }
        }
        stage('Deploy') { 
            steps {
                // 
            }
        }
    }
}

```

```

pipeline {
    agent { docker { image 'maven:3.3.3' } }
    stages {
        stage('build') {
            steps {
                sh 'mvn --version'
            }
        }
    }
}

```

#### Jenkinsfile (Scripted Pipeline)

```
node {  
    stage('Build') { 
        // 
    }
    stage('Test') { 
        // 
    }
    stage('Deploy') { 
        // 
    }
}

```
----------

