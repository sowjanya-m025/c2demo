C1. Create a maven projects with all dependencies required for the application
in CI/CD pipeline.
To run CI/CD pipeline in GitHub
I. Install required tools
II. Install Java (JDK 11 or higher)
o Download from Oracle JDK or OpenJDK
o Set environment variable:
▪ JAVA_HOME = C:\Program Files\Java\jdk-XX
▪ Add %JAVA_HOME%\bin to PATH
III. Install Apache Maven
o Download and extract
o Set:
▪ MAVEN_HOME = C:\apache-maven
▪ Add %MAVEN_HOME%\bin to PATH
IV. Install Visual Studio Code
V. Install VS Code Extensions:
o Extension Pack for Java
o Maven for Java
o Debugger for Java
II. Create Maven Project in VS Code
Open VS Code
Press Ctrl + Shift + P
Type: Java: Create Java Project
Select Maven create from archetype
Choose:
Archetype → maven-archetype-quickstart
Enter details:
Group Id: com.example
Artifact Id: demo (Give any name)
Choose folder → Project will be created automatically
Push the folder to git repository
III. For CI/CD Pipeline (GitHub Actions)
In the local folder
Create a folder by name .github and inside that create worflows folder
and in that create a file maven.yml
Structure:
Localfoldername
-demo
-.github/workflows/maven.yml
Push this to Github repo
IV. Check the actions tab in github for the CI working
Prof. Akshatha M, VVCE
DevOps Lab Cheat Sheet
maven.yml
name: Java CI/CD Pipeline
on:
push:
branches: [ "main" ]
jobs:
build:
env:
runs-on: windows-latest
FORCE_JAVASCRIPT_ACTIONS_TO_NODE24: true
steps:
- uses: actions/checkout@v4
- uses: actions/setup-java@v4
with:
distribution: 'temurin'
java-version: '11'
- name: Build using Maven
run: mvn clean install
working-directory: demo
C2:Integrate communication channel with Jenkins for status of project and also
enable email notification for a build.
Update the plugins in Jenkins
Git Plugin
Pipeline Plugin
Maven Integration Plugin
Create a Jenkinsfile of type file and push it to the repo
Jenkinsfile
pipeline {
agent any
tools {
maven 'Maven3'
}
stages {
Prof. Akshatha M, VVCE
DevOps Lab Cheat Sheet
stage('CHECKOUT') {
steps {
git 'your_github_repo_link'
}
}
stage('Build') {
steps {
dir('demo'){
bat 'mvn clean install'
}
}
}
stage('Test') {
steps {
dir('demo'){
bat 'mvn test'
}
}
}
}
}
In Jenkins goto Manage Jenkins
Tools
Goto Maven installations
Click on Maven installations
Add maven
Name= Maven3
Uncheck auto installations
Maven_Home path C:\Program Files\maven
Save
Now Click on Build Now
For Notification – Slack Tool Usage
Login to slack.com
Create a workspace
Ex: with any name given – it looks like hello-odm9638
Login to https://my.slack.com/services/new/jenkins-ci
Select the hello workspace
Here token will be generated copy that token
Now in Jenkins
New item- freestyle project- ok
In configure- build steps- windows batch command- echo Jenkins
In post-build-action select Slack notification
And check all the options
Save
Prof. Akshatha M, VVCE
DevOps Lab Cheat Sheet
Enable Slack notification in Jenkins
Managejenkins- System
Slack
Workspace= hello-odm9638
Credential= add – secret text
Secret= (copied token)
ID= any name ex: slack-token
Channel= (channel name from slack website)











The missing_scope error confirms exactly what is wrong: your token is valid, but it doesn't have the permission to "write" messages.

Because you are using an xoxp (User) token instead of the old "Legacy" token, Slack is much stricter about permissions.

1. Fix the Permissions (Required)

Go to the [Slack App Settings.](https://api.slack.com/apps)

Click on your App (Jenkins).

On the left menu, click OAuth & Permissions.

Scroll down to Scopes. Look for User Token Scopes (since you are using an xoxp token).

Click Add an OAuth Scope and add:

chat:write (This is the most important one).

Crucial Step: Scroll back to the top of that page and click "Reinstall to Workspace" or "Save Changes." Slack tokens don't update automatically; you have to "Reinstall" to apply the new permissions.

2. Update Jenkins Settings

Once you've updated the permissions in Slack, go back to Jenkins:

Manage Jenkins > System > Slack.

Workspace: hello-odm9638

Credential: (Select your xoxe.xoxp... secret).

Custom slack app bot user: * Uncheck this box (since xoxp is a User token, not a Bot token).

Test Connection.

3. The "Manual Invite" (If Test Connection still fails)

Even with permissions, Slack won't let an app post to a channel unless it's "in" the channel.

Open your Slack app.

Go to your channel (e.g., #general).

Type /invite @Jenkins (or whatever you named your app).

If Slack says "Jenkins is already in the channel," you're good.

If it still says "Failure":

Check the error message again.

If it still says missing_scope, you didn't click "Reinstall App" in Slack after adding chat:write.

If it says channel_not_found, make sure the channel name in Jenkins starts with #.

One last thing: Since your token is an xoxe (expiring) token, it might have actually expired while we were troubleshooting! 

Reinstalling the app in the Slack dashboard will give you

1. Switch to Bot Scopes (The Stable Way)

Go to your Slack App Dashboard and click on your app (hub).

Click OAuth & Permissions on the left.

Scroll down to Bot Token Scopes (NOT User Token Scopes).

Add these three specific scopes:

chat:write

chat:write.public

commands

Scroll to the top and click Reinstall to Workspace.

Copy the Bot User OAuth Token (It will start with xoxb-).

2. Update Jenkins Global Settings

Now, configure Jenkins to act as a Bot:

Manage Jenkins > Credentials:

Edit your slack-token.

Replace the old xoxp token with the new xoxb- token.

Manage Jenkins > System > Slack:

Workspace: hello-odm9638

Credential: Select the updated slack-token.

IMPORTANT: Check the box "Is your Slack app a bot user?" (or "Custom slack app bot user").

Project Channel: #general (make sure the # is there).

Click Test Connection.

3. The "Last Resort" (The Legacy Method)

If the modern app method keeps failing, you can use the exact method from Prof. Akshatha’s notes, which uses a simpler, older integration:

Open this link: https://hello-odm9638.slack.com/services/new/jenkins-ci

Follow the prompts to add the "Jenkins CI" app.

Slack will give you a different token (not xoxb/xoxp).

In Jenkins, UNCHECK "Custom slack app bot user."

Use that legacy token in your credentials.
