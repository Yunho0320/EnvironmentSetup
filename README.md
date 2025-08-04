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
The new pom files are as below: <br>

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

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>1.18.32</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-databind</artifactId>
                <version>2.17.0</version>
            </dependency>
            <dependency>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-api</artifactId>
                <version>2.0.16</version>
            </dependency>
            <dependency>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-classic</artifactId>
                <version>1.5.16</version>
            </dependency>
            <dependency>
                <groupId>org.eclipse.jetty</groupId>
                <artifactId>jetty-server</artifactId>
                <version>9.4.53.v20231009</version>
            </dependency>
            <dependency>
                <groupId>org.eclipse.jetty</groupId>
                <artifactId>jetty-servlet</artifactId>
                <version>9.4.53.v20231009</version>
            </dependency>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>javax.servlet-api</artifactId>
                <version>3.1.0</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
'''

Server pom.xml
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
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jetty</groupId>
            <artifactId>jetty-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jetty</groupId>
            <artifactId>jetty-servlet</artifactId>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
        </dependency>
    </dependencies>
</project>
'''

ui pom.xml
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
    <packaging>pom</packaging>

    <build>
        <plugins>
            <!-- Plugin to run npm commands -->
            <plugin>
                <groupId>com.github.eirslett</groupId>
                <artifactId>frontend-maven-plugin</artifactId>
                <version>1.14.2</version>
                <executions>
                    <execution>
                        <id>install-node-and-npm</id>
                        <goals><goal>install-node-and-npm</goal></goals>
                        <configuration>
                            <nodeVersion>v20.12.0</nodeVersion>
                            <npmVersion>10.5.0</npmVersion>
                        </configuration>
                    </execution>
                    <execution>
                        <id>npm-install</id>
                        <goals><goal>npm</goal></goals>
                        <configuration>
                            <arguments>install</arguments>
                        </configuration>
                    </execution>
                    <execution>
                        <id>npm-build</id>
                        <goals><goal>npm</goal></goals>
                        <phase>compile</phase>
                        <configuration>
                            <arguments>run build</arguments>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <!-- Copy dist folder to server's static resources -->
            <plugin>
                <artifactId>maven-resources-plugin</artifactId>
                <version>3.3.1</version>
                <executions>
                    <execution>
                        <id>copy-vue-dist</id>
                        <phase>prepare-package</phase>
                        <goals><goal>copy-resources</goal></goals>
                        <configuration>
                            <outputDirectory>${project.basedir}/../server/src/main/resources/static</outputDirectory>
                            <resources>
                                <resource>
                                    <directory>${project.basedir}/dist</directory>
                                </resource>
                            </resources>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
'''

Now what we can do is mvn clean package and it will create final JAR in server/target and to check if this jar includes static(frontend) files is going to server/src/main/resources/static after the build - it contains Vue index.html, JS and CSS files.
