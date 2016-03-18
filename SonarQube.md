# [SonarQube](http://www.sonarqube.org/ "SonarQube") #

## Get Started  ##

1.  [Download](http://www.sonarqube.org/downloads/ "Down") and unzip the 	SonarQube distribution (let's say in "C:\sonarqube" or "/etc/sonarqube")

2.  Start the SonarQube server:
 
		# On Windows, execute:
		C:\sonarqube\bin\windows-x86-xx\StartSonar.bat
	 
		# On other operating system, execute:
		/etc/sonarqube/bin/[OS]/sonar.sh console

3.  [Download](http://docs.sonarqube.org/display/SONAR/Installing+and+Configuring+SonarQube+Scanner "Download") and unzip the SonarQube Scanner (let's say in "C:\sonar-runner" or "/etc/sonar-runner")

4.  Update the global settings (server URL) by editing <install_directory>/conf/sonar-runner.properties:

		#----- Default SonarQube server
		#sonar.host.url=http://localhost:9000

5.  Create a new SONAR_RUNNER_HOME environment variable eg:

		SONAR_RUNNER_HOME = D:\Program Files\sonarqube\sonar-runner-2.4

6.  Add the 'install_directory'/bin directory to your path eg:
 
		${SONAR_RUNNER_HOME}/bin (Unix) or %SONAR_RUNNER_HOME%/bin
 
7.  You can check the basic installation by opening a new shell and executing the command sonar-runner -h (on Windows platform the command is sonar-runner.bat -h) . You should get a message like this:
 
		usage: sonar-runner [options]
		 
		Options:
		 -D,--define <arg>     Define property
		 -e,--errors           Produce execution error messages
		 -h,--help             Display help information
		 -v,--version          Display version information
		 -X,--debug            Produce execution debug output

8.  Create a configuration file in the root directory of the project: sonar-project.properties

	**sonar-project.properties**

		# must be unique in a given SonarQube instance
		sonar.projectKey=my:project
		# this is the name displayed in the SonarQube UI
		sonar.projectName=My project
		sonar.projectVersion=1.0
		 
		# Path is relative to the sonar-project.properties file. Replace "\" by "/" on Windows.
		# Since SonarQube 4.2, this property is optional if sonar.modules is set. 
		# If not set, SonarQube starts looking for source code from the directory containing 
		# the sonar-project.properties file.
		sonar.sources=.
		 
		# Encoding of the source code. Default is default system encoding
		#sonar.sourceEncoding=UTF-8
9.  Run the following command from the project base directory to launch the analysis:

		sonar-runner

10.  Browse the results at [http://localhost:9000](http://localhost:9000 "http://localhost:9000") (default System administrator credentials are admin/admin)
