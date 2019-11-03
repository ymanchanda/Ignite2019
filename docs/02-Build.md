## Create new Build Pipeline
- Do this in Azure DevOps
- Point it to your GitHub repo
- Choose the ASP.Net template
- Add a step to run BuildAngular.bat (this can be found in src/PokerLeagueManager.UI.Web/)
- Be sure to run it once before continuing

## Setup a Branch Protection Rule in GitHub
- Find this in Settings
- Create the rule on the master branch
- Be sure to check Include Administrators

## Create, Submit, and Complete a Pull Request
- Suggest changing this file: src/PokerLeagueManager.UI.Web/angular/src/app/navbar/navbar.component.html
- Add some text after "Recent Games", we'll be able to see this change later after we deploy

## Update build to Publish Build Artifacts
- Be sure to use the task called Publish Build Artifacts (NOT Publish Pipeline Artifacts)
- You need to publish these things:
  - 4 WebDeploy packages - by default these are built into the $(Build.ArtifactStagingDirectory)
  - 2 DACPAC - by default these are build deep down in the $(Build.SourcesDirectory)
  - The /deploy folder
  - The build outputs of the PokerLeagueManager.Utilities project (path will be something like /src/PokerLeagueManager.Utilties/bin/Debug)
  - Try and make the build drop clean (see projector slide for example)
- some useful built-in pipeline variables $(Build.SourcesDirectory), $(Build.ArtifactStagingDirectory)
