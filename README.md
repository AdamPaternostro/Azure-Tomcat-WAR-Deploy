# Azure-Tomcat-WAR-Deploy
Shows how to deploy a WAR file to Azure Web App and avoid the WAR file locking issue.

## Assumptions:
1. You have Java installed
2. You have Maven installed.
3. I use VS Code, but any text editor will do.

## Create Web App in Azure
1.

## Create and Clone Repo
![](https://raw.githubusercontent.com/AdamPaternostro/Azure-Tomcat-WAR-Deploy/master/images/step1.png)

1. I used Bitbucket for this demo.  You can use whatever Git repository you would like.

2. Create a repository "tomcatwebapp"

3. Using a prompt, clone to your hard disk: git clone https://adampaternostro@bitbucket.org/adampaternostro/tomcatwebapp.git

## Create Java App
![](https://raw.githubusercontent.com/AdamPaternostro/Azure-Tomcat-WAR-Deploy/master/images/step3.png)

1. CD to your folder: `cd cdtomcatwebapp`

2. Export Maven to your PATH if it is not already in your path: `export PATH=/Users/adampaternostro/apache-maven-3.5.3/bin:$PATH`

3. Generate Java/Maven project: `mvn archetype:generate -DarchetypeArtifactId=maven-archetype-webapp`
   - groupId: myGroupId
   - artifactId: myArtifactId
   - version: 1.0-SNAPSHOT
   - package: myPackage
   
4. Close your prompt.

## Code
1. Open in Visual Studio Code

2. Open your folder

3. Create deploy.sh in the src folder

4. Place the following inside the script
```
#!/bin/bash

echo "STEP 1 (Determine WAR file path)"
warFilePath="target/myArtifactId.war"
echo $warFilePath

echo "STEP 2 (Copy WAR file as ROOT.zip)"
cp $warFilePath ROOT.zip 

echo "STEP 3 (Set variables)"
username="adampaternostro"
password="<<REPLACE ME>>"
sitename="<<REPLACE ME>>"
warZipFile="ROOT.zip"

echo "STEP 4 (POST using cURL)"
curl -X POST -u $username:$password https://$sitename.scm.azurewebsites.net/api/wardeploy --data-binary @$warZipFile

echo "DONE"
```

5.  Change the sitename and change the password you used when creating your Azure Web App.

6. Open the Integrated Terminal in VS Code (Under View | Integrated Terminal Menu)

7. In terminal: Change directories: `cd myArtifactId`

8. In terminal: Export your Maven Path: `export PATH=/Users/adampaternostro/apache-maven-3.5.3/bin:$PATH`

8. In terminal: Build: `mvn package`

9. In terminal: Set execute: `chmod +x ./src/deploy-local.sh`

10. In terminal: Run deploy: `./src/deploy-local.sh`



