
# Android HelloWorld Project From Commandline
##Introduction
Although Android Studio can create projects, the build tools are available in the sdk, and has commandline functions to do this, but it is not always straight forward. Here the PC was running linux and the android sdk was installed but not Android Studio. 

The output from the _sdk android tool_ sometimes refuses to build so some changes are made. I have decided to put those changes and the original command in a script.

###Abbreviations
_[sdk-b-v = sdk build tools version]_

_[gradle-d-v = gradle distribution version]_

_[plugin = Android Plugin Version]_


###Files
- helloscript - a script file for running the create project instructions
- fingerprint.log - fingerprints of the files in the -project edition
- UpdateFiles - a folder containing the files to be updated after the _create project_





###Build script
What the script file does.
- Issue the 'create project' command
 - Uses _gradle_ build type, not _eclipse/ant_ type.
- The script is easily editable for
 - Project Name [and Path]
 - Activity Name 
 - Package Name
 - Android Plugin Version
- Generates a hash code for the output from the _android tool_
 - the hash enables easy verification that the output is as expected
- Implements changes
- Assembles the build 

##Project Organisation
It is expected that the _android tool_ output may change with sdk build tools version, so there will be different editions of this project. Also it would be nice to easily see the differences between the before and after of the changes. _git_ is obviously ideal for both of these.

There are therefore general project specific details and edition specific details. This document is ordered as follows.
- ####Project
- ####Edition
- ####Appendix

##Editions of this project
	sdk-b-v     plugin      gradle-d-v
    22.0.1      1.1.3       2.3-bin



###sdk build versions and git branch
To keep things tidy I am using separate branches for various iterations, with a naming convention.

	Format example:
	sdk build version: 22.0.1
	      branch name: 22_0_1

### Create Project Versions
    sdk-b-v     plugin      gradle-d-v  sha1
    22.0.1      1.1.3       1.12-all    1085fb75d8b9653bf4ac60f345cd01fac89c3c26


#Edition
    This Edition
    sdk-b-v     plugin      gradle-d-v
    22.0.1      1.1.3       2.3-bin
##Script Detail
###Create Project
The effective 'create project' and its output are as follows

- android -v   create project  -n HelloWorld  -a HelloWorldActivity  -k com.example  -p ./HelloWorld  -t 1  -g -v 1.1.3

####Folder/Directory Structure

     {HelloWorld}                   //Project Name
	  - build.gradle
	  - gradlew
	  - gradlew.bat
	  - local.properties
	  
	  > gradle
	    > wrapper
	      - gradle-wrapper.jar
	      - gradle-wrapper.properties

	  > src
	    > main
          - AndroidManifest.xml
	      > java
	        > {com}                 //Package Name
	          > {example}           //Package Name
                - {HelloWorldActivity}.java  //Activity Name
	      > res
	        > drawable-hdpi
	          - ic_launcher.png
	        > drawable-ldpi
	          - ic_launcher.png
	        > drawable-mdpi
	          - ic_launcher.png
	        > drawable-xdpi
	          - ic_launcher.png
            > layout
              - main.xml
            > values
              - strings.xml
     
### Hash         
At this point a hash is generated, which can be checked against the Create Project Versions table.

## Customisations in this Edition

### remove the local.properties file
This file contains information about the local file system, there is a statement within itself regarding security. It is a pointer to the sdk, but this can be achieved using ANDROID\_HOME. If ANDROID\_HOME exists and points to the sdk, then _local.properties_ can be delete, hence this is done in this project. It is also included in the _.gitignore_ file for this project.

###gradle wrapper Version
This is changed to a more recent version.
Note that android plugin and gradle versions must be compatable as per:
http://tools.android.com/tech-docs/new-build-system/user-guide

- version __2.3__ 
- type __bin__ 
- url-protocol __https__

This is done by updating _gradle-wrapper.properties_ in _gradle/wrapper/_ 

###Remove Proguard References
These are removed because the project failed to run, they can be added later.
This is done by updating the _build.gradle_ file in the project folder.

###Create gradle.properties and include daemon
It's normally desirable to use the gradle daemon, hence
_gradle.properties_ which includes the statement _org.gradle.daemon=true_
is added to the project folder.


##Test assembleDebug
Finally the script runs _assembleDebug_


####gradle Output
After the assemble there are two new folders in the project folder
_.gradle_ and _build_ . [_.gradle_ is part of the execution of __gradle__ and is not discussed further.] 
Build contains the files _R.java_ and the _apk_ files.

     {HelloWorld}               //Project Name
      > build
        > generated
          > res
            > source
              > r
                > debug
                  > com...
                    - R.java
                    
        >intermediates
          > ...
                     
        > outputs
          > apk
              - {Project Name}.apk 
              - ... 
          > logs
            - manifest-merger-debug-report.txt
          > reports             //reports does not appear in this project
            > tests
              > debug
                - index.html
                
        > tmp
          > ...
              
##Appendix create test-project
The build in this project, originally, contains a _androidTest_ folder, however it does not contain the _test_ folder. The _android create test-project_ command creates the folders for a full test project, from an existing project, if the file _project.properties_ exists, which it doesn't after a _android create project -g ..._ command.

Again its best to use android studio, but if doing this by hand add the _src/test_ folder manually. Instructions about doing this is in the **Boilerplate** project.


##Appendix Gradle Installation and wrappers
_gradle_ can be installed and/or be used via a wrapper.
The normal location for these is in the home directory in _.gradle_.

              
####home/.gradle/ folder
#####Wrapper
In _.gradle/wrapper/dists/gradle-{version name}_ there are folders named by hash codes. In these folders are the downloaded zip distributions, if the download completed there is a _.zip.ok_ file if incomplete download then these remains a _.part_ file.

Even if a version is downloaded a different wrapper.jar [gradle version] file can cause a new download.

In _.gradle/caches/{version}/plugin-resolution/scripts_
there is one folder per project named 

`build_{hash}/Project_Script` containing files, including _buildscript/cache.properties_ refering to a build/project.

In _.gradle/daemon_ there are log files per session _deamon-####.out.log_ .




