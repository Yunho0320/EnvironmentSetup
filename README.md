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
For this one, we need one parent pom.xml and each pom.xml for each module. 
Parent pom looks like this.
'''
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.yunho-test</groupId>
    <artifactId>MarkdownParser</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>module1</module>
        <module>module2</module>
    </modules>
</project>

'''

Module pom's basic format looks like this.
'''
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.yunho-test</groupId>
        <artifactId>MarkdownParser</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>module1</artifactId>
'''

# Full stack setup - my mistake & solution
For my full stack web setup, I did exactly same as Multi-module project as above, but there was an issue. <br>
I had one 'server' module and one 'ui'module, each supporting backend and frontend respectively. <br>
This ui pom.xml was exactly same as the basic format above, and it didn't really have Maven config in it. <br>
To show my mistake, I'll share as below.

Java backend module
'''
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.yunho-test</groupId>
        <artifactId>MarkdownParser</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>server</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.32</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
'''

UI frontend module
'''
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.yunho-test</groupId>
        <artifactId>MarkdownParser</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>ui</artifactId>

</project>
'''

Main pom.xml
'''
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.yunho-test</groupId>
    <artifactId>MarkdownParser</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>server</module>
        <module>ui</module>
    </modules>
</project>
'''

It works perfectly fine locally but with this setup, to deploy to prod, I need to create a JAR for backend and dist folder, because my dist folder isn't included in the JAR. <br>
So let's tweak it a bit so that when we deploy, it only creates one single JAR that also contains dist (vue build). <br>
