# PostmanIntergrationWithCircle

This Repository describe you how to integrate your Postma API test with CI/CD tool -CircleCI through Newman command line interface.To know more about the architeture of circleCI please visit my blog-https://blog.knoldus.com/integration-of-postman-with-ci-cd-tool-circleci/. 
In this  template , you'll  came to know about CircleCI pipeline for API Test.

 ## Prerequisites
 
A few things are required:
- Basic knowledge of Javascript
- Postman for Desktop installed on your system
- A CircleCI account
- A GitHub account

It is assume that you should have basic knowledge of  API Test in Postman and export that collection or URL in the local folder.

## Setting Up Project 

1. Firstly Push your POstman collection of API into Git Repository.
2. Secondly  signup account in  Circle CI with GitHub -https://circleci.com/signup/
3. Now, go to the Add Projects page on the CircleCI dashboard to add the project. Here you can select the same repo where you pushed your API test.

![image](https://user-images.githubusercontent.com/61194151/112253922-9e576a00-8c85-11eb-9bc3-92098ce1be7e.png)

4. Click Set Up Project.

 ![image](https://user-images.githubusercontent.com/61194151/112253993-cb0b8180-8c85-11eb-9427-4d3a4b4344b1.png)
 
5. On the setup page, click Use Existing Config to indicate that you are setting up a configuration file manually and not using the sample displayed. Next, you get a prompt to either download a configuration file for the pipeline, or to start building.

 ![image](https://user-images.githubusercontent.com/61194151/112254090-fb532000-8c85-11eb-91f0-609827f7be82.png)
 
7. Click Start Building. This build will fail because we have not set up our configuration file yet. We’ll do this in the next step.

 ## Automating the testing process using the Newman orb
 
 The final step is to write the test automation script. This script will use Postman’s CLI sibling, Newman, to run the collection using the exported environment. Luckily, CircleCI has an orb for Newman.

Because newman is a third-party orb, your CircleCI account must be set up to allow it. To change this setting-
- Go to the Organization Settings of your CircleCI account. 
- From the left sidebar, click Security. 
- On the Security Setting page, select Yes to allow third-party orbs in your build.

![image](https://user-images.githubusercontent.com/61194151/112254316-6997e280-8c86-11eb-9713-c48a3b19c150.png)

## Create a configuation file
Create a folder named .circleci at the root of the project and add a configuration file named config.yml inside the folder. In this file, enter the following code:


        
<version: 2.1
orbs:
  newman: postman/newman@0.0.2
  
```
jobs:
  newman-collection-run:
    executor: newman/postman-newman-docker
    steps:
      - checkout
      - newman/newman-run:
          collection: ./Test.postman_collection.json
          additional-options: -r cli,junit
      - run:
          name: Insatll Depenedices
          command: apk add --update ca-certificates openssl && update-ca-certificates
      - run:
          name: Save report
          command: |
            mkdir -p ~/test-results/newman
            cp ./newman/newman-run-report-*.xml ~/test-results/newman/
      - store_test_results:
          path: ~/test-results
      - store_artifacts:
          path: ~/test-results/newman
     
           
workflows:
  version: 2
  Some Random Workflow:
    jobs:
      - newman-collection-run
   
 ````  
  By calling the newman/newman-run command, the script above loads Postman’s Newman orb and runs the collection.It is just that simple.Commit your changes and push to the remote repository to trigger the pipeline build.
 
 ![image](https://user-images.githubusercontent.com/61194151/112262185-40318380-8c93-11eb-8b11-1002ca2430af.png)

To view the test results and all the steps, click the build and expand the Test tab.Also check the test results in artifats.

![ExecutionCircle](https://user-images.githubusercontent.com/61194151/112262685-09a83880-8c94-11eb-8599-a96f362fd1ed.gif)


## Execution Steps
1. You just need to update your postman collection and push that collection into Git Hub.
2. Build will automaticlly trigger in the circelCI and you can check the test results.
