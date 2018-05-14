# Azure-Tomcat-WAR-Deploy
Shows how to deploy a WAR file to Azure Web App and avoid the WAR file locking issue.

## Assumptions:
1. You have Java installed
2. You have Maven installed.
3. I use VS Code, but any text editor will do.

## Create Web App in Azure
1. Open Azure Portal

2. Create a Web App
   ![](https://raw.githubusercontent.com/AdamPaternostro/Azure-Tomcat-WAR-Deploy/master/images/step7.png)
   
3. Give it a name and create a App Service.  
   ![](https://raw.githubusercontent.com/AdamPaternostro/Azure-Tomcat-WAR-Deploy/master/images/step8.png)

4. Open the Web App

5. Set deployment credentials.
   ![](https://raw.githubusercontent.com/AdamPaternostro/Azure-Tomcat-WAR-Deploy/master/images/step9.png)
   - Set your username
   - Set your password

6. Set these settings.
   ![](https://raw.githubusercontent.com/AdamPaternostro/Azure-Tomcat-WAR-Deploy/master/images/step10.png)
   - Set Java Version to Java 8
   - Set Java minor version to Newest
   - Set Java web container to Tomcat 8.5... (whatever you like) 
   - Set Platform to 64-bit
   - Set ARR Affinity to Off





## Create and Clone Repo
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

3. Create deploy-local.sh in the src folder

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

5. Change the sitename and change the password you used when creating your Azure Web App.

6. Open the Integrated Terminal in VS Code (Under View | Integrated Terminal Menu)

7. In terminal: Change directories: `cd myArtifactId`

8. In terminal: Export your Maven Path: `export PATH=/Users/adampaternostro/apache-maven-3.5.3/bin:$PATH`

8. In terminal: Build: `mvn package`

9. In terminal: Set execute: `chmod +x ./src/deploy-local.sh`

10. In terminal: Run deploy: `./src/deploy-local.sh`

11. Open your Azure website and you should see "Hello World"

12. Create deploy-vsts.sh in the src folder

13. Place the following inside the script:
```
#!/bin/bash

echo "STEP 1 (Determine WAR file path)"
warFilePath="$AGENT_RELEASEDIRECTORY\_$BUILD_DEFINITIONNAME\drop\myArtifactId\target\myArtifactId.war"
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

14. Change the sitename and change the password you used when creating your Azure Web App.

15. Check in your code.  Commit and then Push to Bitbucket.  (You can ignore the WAR and Root.zip files)

## Automated Build
If you get prompted for a Build agent anything in this process, create a new "Hosted" one.

1. Create a VSTS project (e.g. https://paternostromicrosoft.visualstudio.com/)

2. Create a new project called "tomcatwebapp".  It does not matter the source control since we are using an external one.

3. Click on Builds menu and select Builds

4. Click the "New Defination" button

5. Select Bitbucket Cloud.  You will need to authorize this.  You can use your username and password for the time being.  You should use an OAuth token for production.

6. Select your repository and master branch.  
   NOTE: If you do not see this then you might need to go back in and hit refresh.  The first time can be tricky.  You can also click on the Gear icon at the top of VSTS.  Then click on Services.  You can delete or reset your connection to Bitbucket here.  Then start back at step 3 (create a new build).
   
7. Click the "Empty process" near the top.

8. Click the + on the Phase.  To add a task.

9. Search for Maven and click add.

10. Change the pom.xml location to myArtifactId/pom.xml

11. Uncheck the JUnit test (optional)

12.  Press Save and Queue (give it a name).  You can then click on the Build and view it.  An Item will appear near the top of the web page with the Build number.  It is a link.

## Automated Deployment



