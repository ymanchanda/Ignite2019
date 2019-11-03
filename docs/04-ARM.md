## Create a new build yaml for CI builds
- We'll leave the existing one for PR builds, create a new longer-running one for CI builds
- Copy the existing azure-pipelines.yaml and give it a new name (e.g. azure-pipelines-ci.yaml)
- On the PR yaml put trigger: none in there so it only runs on Pull Requests
- On the CI yaml put pr: none (leave the master branch trigger in there)

## Fix up the ArmTemplate.json
- it's in the /deploy folder
- anywhere you see "foo" you need to update it
- parameterize the password so we're not checking passwords in
- any ARM functions you want to use need to go inside []
- some ARM functions you can use:
  - "[parameters('appname')]"
  - "[resourceGroup().location]"
  - "[resourceGroup().name]"
  - "[concat('foo', parameters('blah'), '-commands')]
- depending on how you choose to define your resource names you may need to make other changes, be sure to review the whole thing
- you can test it out using the Cloud Shell in the azure portal (upload the template to the cloud shell)
  - Set-AzContext "DevOps CAT Workshops"
  - New-AzResourceGroup -Name "024dylantest" -Location "westus2"
  - New-AzResourceGroupDeployment -ResourceGroupName "024dylantest" -TemplateFile "ArmTemplate.json" -appname "024dylantest"


## Update the CI Build to provision an environment and run integration tests
- Add steps to the CI build to do an ARM Template Deployment
- Add steps to deploy the application (almost exactly like in the Release pipeline)
- Run the Generate Sample Data command - this is our integration test
- Add a ARM Deploy task and choose action Delete Resource Group
  - For the delete RG task you NEED to add a location property, even though it's not obvious (likely bug)
- this will take you a good long while to get working right - keep at it! Ask for help if stuck


## Update your Release Pipeline to apply the ARM Template
- This eliminates configuration drift

## Add a PROD Environment to release pipeline
- Need to variabilize everything that is different between environments
- No need to manually create Azure Resources this time - just let the ARM template do all the work
- Don't run the Generate Sample Data command in PROD, also don't pass the flag to CreateNewDatabase in PROD
