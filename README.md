# Azure-Tomcat-WAR-Deploy
Shows how to deploy a WAR file to Azure Web App and avoid the WAR file locking issue.

Assumptions:
1. You have Java installed
2. You have Maven installed.
3. I use VS Code, but any text editor will do.

1. I used Bitbucket for this demo.  You can use whatever Git repository you would like.

2. Create a repository "tomcatwebapp"

3. Using a prompt, clone to your hard disk: git clone https://adampaternostro@bitbucket.org/adampaternostro/tomcatwebapp.git
  
4. CD to your folder: cdtomcatwebapp

5. Export Maven to your PATH if it is not already in your path: export PATH=/Users/adampaternostro/apache-maven-3.5.3/bin:$PATH

6. Generate Java/Maven project: mvn archetype:generate -DarchetypeArtifactId=maven-archetype-webapp
   groupId: myGroupId
   artifactId: myArtifactId
   version: 1.0-SNAPSHOT
   package: myPackage
   
7. Close your prompt.

8. Open in Visual Studio Code

9. Open your folder

10. Create deploy.sh in the src folder

11. Place the following inside the script
```
#!/bin/bash

# Pushes out the WAR file to Azure Web App and avoids the locking issue when 
# more than one server is running.  The servers can have a race condition where
# they all try to lock the WAR file to unzip it for Tomcat.

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

12.  Change the sitename (later you will create one) and change the password.
