# CI/CD Azure Pipelines for API

## What is *Http* and *Rest API's* ?

**HTTP** is a communications protocol that transports messages over a network. **Rest** is a protocol to exchange any(*XML or JSON*) messages that can use HTTP to transport those messages.

A RESTful API is an architectural style for an application program interface (API) that uses HTTP requests to access and use data. That data can be used to GET, PUT, POST and DELETE data types, which refers to the reading, updating, creating and deleting of operations concerning resources.

---------------

## About this exercise

In this lab we will be working on API code Base, **Backend Code base** 
### **Backend Code Base:**

Previously we developed a base structure of an api solution in Asp.net core that have just two api functions GetLast12MonthBalances & GetLast12MonthBalances/{userId} which returns data of the last 12 months total balances.

![](/BBbankAPI/images/12m.jpg)


There are 4 Projects in the solution. 

*	Entities : This project contains DB models like User where each User has one Account and each Account can have one or many Transactions. There is also a Response Model of LineGraphData that will be returned as API Response. 

*	Infrastructure: This project contains BBBankContext that service as fake DBContext that populates one User with its corresponding Account that has three Transactions dated of last three months with hardcoded data. 

* Services: This project contains TransactionService with the logic of converting Transactions into LineGraphData after fetching them from BBBankContext.

* BBBankAPI: This project contains TransactionController with 2 GET methods GetLast12MonthBalances & GetLast12MonthBalances/{userId} to call the TransactionService.

![](/BBBankAPI/images/4m.png)

For more details about this base project See: https://github.com/PatternsTechGit/PT_ServiceOrientedArchitecture

-----------

## In this exercise

* We will create free Web App using *Azure App Service*
* We will create Continuos Integrated *CI Build Pipeline*
* We will set a stage ( necessary permissions) before release pipeline.
* We will create  Continuous Deployment *CD Release Pipeline*

*Note: At the moment, we will create whole scenario for Test environment and in the end mimic the scenario for Production environment*
### Step 1: Creating Web App using Azure 

 - Open Azure portal and search for Azure App services 
 ![](/BBBankAPI/images/1.png)

 - Create new app service
 - Select your subscription and resource group
 -  Give a meaningful website name for URL. In our case it is BBBankApiTest and our URL will be https://bbbankapitest.azurewebsites.net
![](/BBBankAPI/images/2.png)

- Our publish version will be a *Code* written in 
- Select *Runtime stack* as *.Net 6* as our API is written in .Net
![](/BBBankAPI/images/5.png)  

- Select size of memory according to need and pricing model 
![](/BBBankAPI/images/3.png)

- Azure gives you full opportunity to test and run your web apps free or at cheaper costs in Dev/Test environment. For this lab we will use free version of Azure Dev/Test which gives us 60 minutes per day free compute time. 
![](/BBBankAPI/images/4.png)

- Let all other section remain as default and click *Review and Create*. It will successfully create you WebAPI. 

*Note: Stop this WebAPI at the moment which will save your compute time while we are creating CI/CD Pipelines. Later we will turn it on when needed.* 

--------------------

### Step 2: Creating Azure Continuous Integrated Pipeline

- Go to Azure devops portal and within your organization select *Pipelines* from the left menu
![](/BBBankAPI/images/6.png)
> If you don't setup your devops account or your organization, you can follow the steps in our lab [Setting up Project environment in Azure DevOps](https://github.com/PatternsTechGit/PT_GithubRepos)

- We are not using YAML in this lab so we will Use the Classic Editor 
![](/BBBankAPI/images/7.png)

- Select github as a source and connect your github to your Azure pipeline via service connections 
![](/BBBankAPI/images/8.png)

- Use OAuth or personal access token from github to connect you account. [Follow this link](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token) to get personal access token from github. 
![](/BBBankAPI/images/9.png)

- Select your github repository and branch you want to authorize it to. As we are creating our Test environment at the moment, we will use test branch *API_CI-CD_Test* rather than main branch. 
![](/BBBankAPI/images/11.png)
- Click create tab

- Now our pipeline is created. Give a meaningful name to your pipeline.
![](/BBBankAPI/images/12.png)

- To assign tasks to agent click '+' . Here we can use predefined tasks for any .Net Application. 
![](/BBBankAPI/images/13.png)

- It will create 5 tasks for you:

> Restore: It will restore dependencies.

> Build: Build your project

> Test: Test with the .NET Core CLI task or a script

> Publish: It will publish the output artifact

> Publish Artifact: It will publish your artifact in drop location which will be used by Release pipeline in further stages. 

![](/BBBankAPI/images/14.png)

- Enable automatic trigger for this pipeline. It will always run this pipeline whenever there is any change in that particular branch of github repository. 
![](/BBBankAPI/images/14-1.png)

- Save and Queue your pipeline and run it in windows environment. 
![](/BBBankAPI/images/15.png)

- After successfully running each step, it will drop your artifact at the provided location. 
![](/BBBankAPI/images/16.png)

- It shows the the job is done and we have created our CI pipeline successfully.

----------------------
### Step 3: Giving permissions to Azure devops organization in Azure WebApi

Before moving on to Continuous Deployment CD we will give permission to our organization to allow our Pipeline able to make changes in API.

- Open Azure portal and select API we created previously
- In access control(IAM) section will add a role assignment
![](/BBBankAPI/images/18.png)

- We have to give Owner role to our organization so it can make all the changes in API. Right now we want our pipeline able to publish artifact in our API
![](/BBBankAPI/images/19.png)

- Select a member and search your organization name and assign permissions to it. 
![](/BBBankAPI/images/20.png)

-------------------------------

### Step 4: Creating Continuous Deployment CD Pipeline

- Go back to azure devops portal and select Release Pipeline from the left menu  and add new pipeline
![](/BBBankAPI/images/21.png)

- Give a meaningful name to you pipeline and assign tasks in Stage 1 of release pipeline. 
- Here we already have template of task to deploy your artifact in Azure app services. You can also select Empty Job and do it manually. 
![](/BBBankAPI/images/22.png)

- First we will name this stage
- Then select your subscription on which your API is hosted
- Then select the type of app
- Select the name of you API which you have given in App Services
![](/BBBankAPI/images/23.png)

- Keep the remaining items as default and save your stage.
- Now we will add the artifact location from where our stage will get the artifact and publish it in our API
![](/BBBankAPI/images/25.png)

- Our location of artifact is *Build* pipeline which we have created in second step.
- We will select the source from where the artifact is coming and click *Add*
![](/BBBankAPI/images/26.png)

- To automatically trigger this pipeline we will select the icon shown below and enable automatic trigger. This will trigger this pipeline whenever there is any new artifact in dropped from Build pipeline.
![](/BBBankAPI/images/30.png)

- Now save and run this pipeline. It will follow all steps and will publish artifact to API
![](/BBBankAPI/images/27.png)

*We have successfully created our CD pipeline*

--------------------

## Final output

- To check the functionality of this make any changes in your code editor and commit changes in github account. It will automatically trigger CI Pipeline and CI pipeline will automatically trigger CD pipeline. CD pipeline will then publish artifact to API

- To see the result copy URL of Web API we created previously 
![](/BBBankAPI/images/28.png)

- This is the URL we created for this lab https://bbbankapitest.azurewebsites.net/

- Give the app extension in front of URL and check the output https://bbbankapitest.azurewebsites.net/api/Transaction/GetLast12MonthBalances/

- We can now see output result of our API as seen below
![](/BBBankAPI/images/29.png)

---------------------
### *Note: For production environment we can copy all these steps and select either we want to manually trigger the production environment or automatically*
