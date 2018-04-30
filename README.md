# Azure Voting App
This is the original Azure Voting App from https://github.com/Azure-Samples/azure-voting-app-redis. This repo added extra files from https://github.com/Azure-Samples/python-docs-hello-world to the app, so it can be deployed directly to Azure App Service. The instruction below shows how to deploy this app from a git clone of this repo in a Windows console.

## Prerequisites
- [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)
- [Git](https://git-scm.com/)
- [Python](https://www.python.org/downloads/) (Optional. You don't need Python unless you want to run this app locally.)

## Instructions

The Azure Voting App uses REDIS cache for backend. Create an Azure redis cache and record the host name and the key (password). You should have an Azure resource group created already. Note down the name of App Service plan and the name of the Web App you are going to create. Replace `xxxxx` with real values as below.
```
set REDIS=xxxxx.redis.cache.windows.net
set REDIS_PWD=xxxxx
set ResourceGroup=xxxxx
set AppPlan=xxxxx
set AppName=xxxxx
```

Create Azure deployment username and password. Skip this step if you had this set up before.
```
set GitUserName=xxxxx
set GitPassword=xxxxx
az webapp deployment user set --user-name %GitUserName% --password %GitPassword%
```

Next, create an Azure web app with local git deployment enabled. It will ask you for GitPassword when you do `git push` for the first time.
```
az appservice plan create -g %ResourceGroup% -n %AppPlan%
az webapp create -g %ResourceGroup% -p %AppPlan% -n %AppName% -l
git remote add %AppName% https://%GitUserName%@%AppName%.scm.azurewebsites.net/%AppName%.git
git push %AppName% master
```

Set REDIS service in app settings of the web app we just created.
```
az webapp config appsettings set -g %ResourceGroup% -n %AppName% --settings REDIS=%REDIS% REDIS_PWD=%REDIS_PWD%
```

Now, browse to the web app (http://{AppName}.azurewebsites.net/), you should see it up and running with voting for cats and dogs. You can change it to vote for something else, for example, vote for ice cream and yogurt.
```
az webapp config appsettings set -g %ResourceGroup% -n %AppName% --settings TITLE="Like Desserts?" VOTE1VALUE="ICE CREAM" VOTE2VALUE="YOGURT"
```