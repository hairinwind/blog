## jacoco coverage
Here is the configuration in pom.xml. It needs work with surefire together.
```
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.4</version>
    <executions>
        <!--
            Prepares the property pointing to the JaCoCo runtime agent which
            is passed as VM argument when Maven the Surefire plugin is executed.
            -->
        <execution>
            <id>pre-unit-test</id>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
            <configuration>
                <!-- Sets the path to the file which contains the execution data. -->
                <destFile>${project.build.directory}/coverage-reports/jacoco-ut.exec</destFile>
                <!--
                    Sets the name of the property containing the settings
                    for JaCoCo runtime agent.
                    -->
                <propertyName>surefireArgLine</propertyName>
            </configuration>
        </execution>
        <!--
            Ensures that the code coverage report for unit tests is created after
            unit tests have been run.
            -->
        <execution>
            <id>post-unit-test</id>
            <phase>test</phase>
            <goals>
                <goal>report</goal>
            </goals>
            <configuration>
                <!-- Sets the path to the file which contains the execution data. -->
                <dataFile>${project.build.directory}/coverage-reports/jacoco-ut.exec</dataFile>
                <!-- Sets the output directory for the code coverage report.
                <outputDirectory>${project.reporting.outputDirectory}/jacoco-ut</outputDirectory>
                -->
                <excludes>
                    <exclude>**/*com/central1/decm/common/domain/**</exclude>
                    <exclude>**/*com/central1/decm/common/mapper/**</exclude>
                    <exclude>**/*com/central1/decm/common/codes/**</exclude>
                    <exclude>**/*com/central1/decm/common/configuration/**</exclude>
                    <exclude>**/*com/central1/decm/common/constants/**</exclude>
                    <exclude>**/*com/central1/decm/common/exceptions/**</exclude>
                </excludes>
            </configuration>
        </execution>
    </executions>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.15</version>
    <configuration>
        <!-- Sets the VM argument line used when unit tests are run. -->
        <argLine>${surefireArgLine}</argLine>
        <!-- Skips unit tests if the value of skip.unit.tests property is true -->
        <skipTests>${skip.unit.tests}</skipTests>
    </configuration>
</plugin>
```
reference:
https://sonarcloud.io/documentation/analysis/coverage/  
https://docs.sonarqube.org/display/PLUG/JaCoCo+Plugin  
https://github.com/hborders/jacoco-coverage-example/blob/master/pom.xml  
https://stackoverflow.com/questions/27799419/maven-jacoco-configuration-exclude-classes-packages-from-report-not-working  
https://www.eclemma.org/jacoco/trunk/doc/report-mojo.html  
https://ccbill.com/blog/code-coverage-with-surefire-and-jacoco  