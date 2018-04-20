# Deploy, scale and monitor SimplCommerce on Azure

## Run SimplCommerce locally

Step 1: Clone the source code https://github.com/simplcommerce/SimplCommerce

Step 2: Open SimplCommerce.sln in Visual Studio 2017 and build. Open the Task Runner Explorer and make sure that the 'copy-module' gulp task is called. See http://docs.simplcommerce.com/en/latest/development-tips/

Step 3: In the SimplCommerce.WebHost project, open file appsettings.json and make sure its credential can connect to SQL Server

Step 4: In the Package Manager Console, run 'Update-Database' command to create tables.

Step 5: Execute the file Database/StaticData.sql to the created database.

Step 6: Control + F5 to launch the website

## Deploy SimplCommerce to Azure Web App

Step 1: Login to https://portal.azure.com, contact us if you don't have a subscription

Step 2: Create a resource group, name gap2018 for example

Step 3: Create an Azure SQL Database

Step 4: Create an Web App

Step 5: Right click on the SimplCommerce.WebHost project, choose publish, App Service, choose the Web App you have just created.
In the setting. Copy the connection string of your Azure SQL Database and update the user and password then click Publish

Step 6: Open SQL Management Studio and connect to SQL Azure, delete all recore in Core_User table and execute the Database/StaticData.sql

Step 7: Refresh SimplCommerce website and enjoy.

## Enable Application Insights

In the Azure Portal, go to the Web App, then follow the instruction on the screen to enable Application Insights. It should be very easy.
