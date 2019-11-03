## Create an Azure Environment
- For now we're going to be manually creating the Azure resources in https://portal.azure.com
- Start by creating a new Resource Group - prefix the name with your student number
- Put all resources in the Azure Region that was assigned to you
- You will need to create:
  - 4 Web Apps and 1 App Service Plan that they will all belong to (you will be prompted to create the App Service Plan when you create the first Web App)
    - the 4 web apps are for the commands service, events service, queries service, and UI
    - the web app names need to be globally unique (they will become DNS names)
    - suggest you choose a common prefix with your number - and tack on -events, -queries, etc to the end
  - 1 Application Insights instance (all Web Apps should point to the same App Insights instance)
  - 2 SQL Azure databases and 1 SQL Server (You will be prompted to create the SQL Server when you create the first database)
    - the SQL Server name needs to be globally unique, but the DB names do not.  Suggest you name the databases Events + Queries
    - When creating the first DB be sure to make it a public endpoint and check the box to allow all Azure IP's and your Client IP to access it
- DON'T FORGET TO PUT EVERYTHING IN THE RIGHT REGION
    
- When done your resource group should list 9 resources (see project slide for example)

- PLEASE choose the right resource sizes when creating things:
  - Databases: Standard S1 - 20 DTU
  - App Service Plan: B1

## Configure Web Apps
- You need to create some App Settings and Connection Strings on the 4 Web Apps
- You do this by going to the Configuration tab in side each web app
- Commands Web Service
  - App Setting: AppInsightsKey
  - App Setting: QueryServiceUrl
  - Connection String: default -> EventStore Database
- Events Web Service
  - App Setting: AppInsightsKey
  - Connection String: default -> QueryStore Database (THIS IS NOT A TYPO - Events Service connects to Query DB)
- Queries Web Service
  - App Setting: AppInsightsKey
  - Connection String: default -> QueryStore Database
- UI Web App
  - App Setting: AppInsightsKey
  - App Setting: CommandServiceURL
  - App Setting: QueryServiceURL
  
- Don't forget to fill in the password on the db connection strings
- YOU MUST USE HTTP NOT HTTPS on all URL's

## Create Release Pipeline in Azure DevOps
- Add your build as the artifact sourcce
- Add a branch filter to the artifact so it only picks up master builds
- Start with Empty Job instead of picking a template (it's easier in this case)
- You'll need 8 tasks in the release:
  - 4x Deploy Azure App Service
  - 2x Deploy SQL Azure Dacpac
  - 2x Command Line
- For the App Service deploys you point it at the Zip file in your build artifacts
- For the DB deploys you point it at the dacpac file
- The first command-line task is to register the Events Service as a Subscriber (replace tokens with your values)
  - PokerLeagueManager.Utilities.exe CreateEventSubscriber {your database server}.database.windows.net {your events database name} {db admin user} {db admin password} http://{events service}.azurewebsites.net
- The 2nd command-line task is to generate some sample data - this will also tell you if everything is working right
  - PokerLeagueManager.Utilities.exe GenerateSampleData http://{commands service}.azurewebsites.net {number of games to generate - try 5}
