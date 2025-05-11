# Environment setup
## Java Setup
1. Download JDK
2. Set environment variable JAVA_HOME to where jdk is saved
3. Add %JAVA_HOME%\bin to PATH variable
4. On Command Prompt type **where java** to see whre java.exe is. 

## Maven Setup
1. Download Maven (bin.zip)
2. Set environment variable M2_HOME to where maven is saved
3. Add %M2_HOME%\bin to PATH variable
4. On Command Prompt type **mvn -version** to see where maven is

## Make IntelliJ project Maven project
1. Add pom.xml
2. On the right bottom of IntelliJ, you'll see Maven 'projectName' build script found' - Load Maven Project. Simply press the button.

Where is .m2 folder?
It's in C:\Users\<YourUsername>\.m2\. If it's not there, simply run mvn clean install, and it'll create .m2 folder.

# Project setup (Multi-module project)
1. Create project level pom.xml and one for each module - In Project Structure > Modules, you can see that intelliJ has automatically creation of modules. You can also see .iml files creates for each module- module creation done <br/>
project pom.xml looks like this: Note how it has reference to modules inside the project. This needs to be changed when we change the module (refactor will automatically do it) and when we add modules, this part also needs to be changed
```
<!-- This is the Parent pom -->
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>

    <modules>
        <module>example_1</module>
        <module>example_2</module>
    </modules>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>
</project>

```

Module pom.xml looks like this 
```
<!-- MyProject/pom.xml -->
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>example_1</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>
</project>
```
2. In Project Structure > Modules, click one of the modules, then Paths.Set the Output path to like this C:\Dev\Workspace\personal_projects\ModularProgramming\example_1\target\classes - target directory done.
3. .idea files explain:
* gitignore: git ignore rules
* compiler.xml - compiler settings such as JDK version, annotation processing etc - this is machine specific
* JarRepositories - Tracks external JAR repositories - this is user specific
* kotlin.xml - Kotlin compiler settings
* misc.xml - Basic project-level settings such as encodin, language level
* modules.xml - list of modules and their paths (.iml files)
* workspace.xml - UI layout, editor state, breakpoints, open files

Remove kotlin.xml and put all of them into gitignore except modules.xml.We also want to git ignore Maven build output(target directory) and some OS specific files & logs, and temp files.
Write gitignore as below & push it: 
```
# Default ignored files
/shelf/
/workspace.xml

# Ignore all the files in idea
.idea/*

# include modules.xml in idea
!.idea/modules.xml

# Ignore Maven build output
target/

# OS-specific
.DS_Store
Thumbs.db

# Logs and temp
*.log
*.tmp
```
If you accidentally pushed your first project including the files in .idea, they will still be tracked even though they are included in gitignore. <br />
We need to untrack them. <br/>
I used Baeldung as a reference to solve this issue: <b>https://www.baeldung.com/ops/git-remove-file-without-deleting-it#:~:text=Using%20the%20git%20rm%20%E2%80%93cached,keep%20the%20local%20file%20untouched.</b>
```
git rm -r --cached .idea

// git rm: telling git to remove a file from tracking
// -r: recusively so that it applies to all files inside .idea
// --cached: telling git to remove from Git's version control but keep the files on our disk
```
For my case, I accidentally commited all the files in .idea after the initial push so I had to do as below:
```
git reset HEAD  // This unstages all the files that are staged for commit
```
When I run git status, I'm getting these messages that it's good because I can ./idea is now untracked.
```
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        deleted:    .idea/ModularCoding.iml
        deleted:    .idea/misc.xml
        deleted:    .idea/modules.xml
        deleted:    .idea/vcs.xml

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        .idea/
        example_1/example_1.iml
        example_1/pom.xml
        example_1/target/
        example_2/example_2.iml
        example_2/pom.xml
        pom.xml
```
However, in intelliJ, under commit, I still see all the files in .idea under Unversioned Files & Changes.
So I pushed my gitignore file again but they are still showing up.
Turned out intelliJ showing files in Commit UIs (under Changes & Unversioned Files) has got nothing to do with Git. So our implementation is correct and has worked. <br/>
But it was a bit annoying that I still see them under Unversioned Files but the solution was quite simple.... When I right clicked each file, there was gitignore button and I could put them in exclude file and they no longer showed up

