## install dependencies locally
this command can be used to ensure you have all of your dependencies installed locally before you begin to work offline.
```
mvn dependency:go-offline
```
## maven download source jar and javadoc
```
mvn dependency:sources dependency:resolve -Dclassifier=javadoc
```
https://www.baeldung.com/maven-download-sources-javadoc 

## run Test
http://appsdeveloperblog.com/running-a-single-unit-test-with-maven/
run all test in one class 
```
mvn test -Dtest=UserServiceImplTest
```
Run one test method in one class
```
mvn test -Dtest=UserServiceImplTest%testFunction1
```

## maven build non-java project as a artifact  
My project has a sub project for db script. To package it as a artifact, use maven-assembly-plugin
```
<plugin>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.1.1</version>
    <configuration>
        <runOnlyAtExecutionRoot>true</runOnlyAtExecutionRoot>
        <descriptors>assembly/dep.xml</descriptors>
    </configuration>
    <executions>
        <execution>
            <id>archive-properties-scripts-and-db-scripts</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```
The assembly config file is "assembly/dep.xml"
```
<assembly xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2 http://maven.apache.org/xsd/assembly-1.1.2.xsd">
    <id>src</id>
    <formats>
        <format>tar.gz</format>
    </formats>
    <fileSets>
        <fileSet>
            <directory>${project.basedir}</directory>
            <includes>
                <include>config/*</include>
                <include>db/*</include>
                <include>script/*</include>
            </includes>
            <useDefaultExcludes>true</useDefaultExcludes>
        </fileSet>
    </fileSets>
</assembly>
```

