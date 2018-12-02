Download the sonar-cis-plugin.zip and extract it somewhere
download the sonarqube from here: https://github.com/SonarSource/sonarqube

Download/install gradle
Download/install JDK
Download/install maven https://maven.apache.org/download.cgi (apache-maven-3.5.4-
bin.zip)

Create and Point path variables in environment variables....
example:
GRADLE_HOME: C:\Program Files\Android\Android Studio\gradle\gradle-3.2\bin (i already had gradle as part of android, so your path will be different)
JAVA_HOME: C:\Program Files\Java\jdk1.8.0_131
M2_HOME: C:\Program Files\Apache\apache-maven-3.5.4

check that the steps above were successful by gradle -v to show version in CMD

Download MySQL (you can download/use Xampp since it comes with both the MySQL connector and a friendly UI as well phpmyadmin, or you can use MySQL workbench)
Note: To use phpmyadmin, apache in xampp has to also be running! in browser, navigate to: localhost/phpmyadmin/

Create a schema called "sonarqube"
import the sql files from ddl folder and execute them under the sonarqube schema you created. 
C:\Users\...\sonar-cis-plugin-master\src\main\ddl

these are the 3 tables that are used for queries in some of the complexity metrics java files. 

Now:
Open CMD and cd to the SonarQube directory: cd C:\Users\...\...\sonarqube-master
Run Gradle build CMD inside sonar cube build directory: gradlew build

Import the sonar-cis-plugin project for example in eclipse (or any other IDE of choice)
open terminal in the IDE from the project and run: mvn clean install
...It will download a bunch of maven dependencies

To run the SonarQube locally, find the StartSonar.bat file in the Gradle SNAPSHOT that was
generated. Gradle will have created a snapshot zip file of the project, which contains that bat
file in there (just search for the file in explorer and will find it wherever the zip was placed, then
extract the snapshot to any location) and navigate to:
C:\Users\...\Documents\sonarqube-master\sonarqube-7.4-SNAPSHOT\bin\windows-x86-64

right click StartSonar and run (may need to run as admin)
(LEAVE the terminal that runs OPEN! otherwise, if you close it you will have to restart your computer everytime to start it again!)

open browser and navigate to: localhost:9000

Login as user: admin password: admin (just an admin profile), and when this comes up, create a
token and copy the token provided somewhere safe

Open terminal from the project again and run: mvn install

A jar file will be generated in “target” folder

Copy that jar file under:
C:\Users\...\sonarqube\sonar-application\build\distributions\sonarqube-7.4-
SNAPSHOT\extensions\plugins

if start sonar is still running from earlier, type ctrl c and wait for it to stop in the CMD/terminal...one it stops it will ask you to terminate and then you can rerun start sonar.bat again

Start SonarQube (again) from the SNAPSHOT folder now
C:\Users\...\Documents\sonarqube-master\sonar-application\build\distributions\sonarqube-
7.4-SNAPSHOT\bin\windows-x86-64

Run the following in the terminal (for login replace it with your token instead you saved earlier).
Make sure Sonar is still running, otherwise, it won’t know what localhost:9000 is!

mvn sonar:sonar -Dsonar.host.url=http://localhost:9000 -Dsonar.login=YOUR_TOKEN_GOES_HERE

Login (i.e. admin) again at http://localhost:9000
Navigate to “Project” and you will notice the project has been scanned!

click on the project and you will see "measures". those contain the metrics. 

everytime a new metric is written/developed in C:\Users\...\sonar-cis-plugin-master\src\main\java\edu\umich\cis\measures, 
you have to run in the IDE terminal of the project "mvn install"

and then you have to copy the jar file as noted above, and then run the scan again with that command above as well.
then your new metric will show up in the measures section of the project on the webpage, such as ComputeChangeFrequency.java, etc...

----

About developing new metrics:

To add database tables, add the sql file under C:\Users\...\sonar-cis-plugin-master\src\main\ddl folder and make sure you are creating any tables under the sonarqube schema. 
The jdbcconnection.java file is located here: C:\Users\...\sonar-cis-plugin-master\src\main\java\edu\umich\cis
currently its set to root as user name, with no password. if you have not created user/password for your computer, then leave them as is. That is the default connection.

Develop/Add java files for new metrics here: C:\Users\...\sonar-cis-plugin-master\src\main\java\edu\umich\cis\measures

You may notice, for example in the ComputeComplexityDelta.java or ComputeChangeFrequency.java, 
CoreMetrics.NCLOC_KEY, ExampleMetrics.FILE_ID.key() and CoreMetrics.COMPLEXITY_KEY, ExampleMetrics.FILE_ID.key()

these are used as input metrics from pre-existing metrics to make things easier on creating new metrics. you can notice NCLOC for example is a lines of code metric which is seen under the project measure on localhost:9000

for the list of all api/key metrics you can use as inputs, the file is called CoreMetrics.java and is in the sonarqube you downloaded, or here:
https://github.com/SonarSource/sonarqube/blob/master/sonar-plugin-api/src/main/java/org/sonar/api/measures/CoreMetrics.java

as for ExampleMetrics file, its located under the java files: C:\Users\...\sonar-cis-plugin-master\src\main\java\edu\umich\cis\measures
