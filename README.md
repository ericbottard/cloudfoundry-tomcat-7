# CloudFoundry Tomcat 7
A modified version of Tomcat v7.0.29 to run on [CloudFoundry](http://www.cloudfoundry.com), as a standalone app, until Tomcat7 is available as a runtime.

Bascially, this is just what is described [here](http://blog.cloudfoundry.org/2012/06/18/deploying-tomcat-7-using-the-standalone-framework/) all done for you.

## Usage
### As a script
If you're working with Maven, read on. Else, just

	$git clone git@github.com:ericbottard/cloudfoundry-tomcat-7.git
	$cd cloudfoundry-tomcat-7
	<Replace webapps/ROOT with your own app>
	$vmc push
	
### Maven
A tar.gz package of the contents of this repository is being served from [here](http://ericbottard.github.com/cloudfoundry-tomcat-7/org/cloudfoundry/samples/tomcat7-standalone/7.0.29/tomcat7-standalone-7.0.29.tar.gz) making [http://ericbottard.github.com/cloudfoundry-tomcat-7/](http://ericbottard.github.com/cloudfoundry-tomcat-7/) appear as a Maven repository. You can have your project download this package and put your web application in it thanks to the Maven assembly project. Just do the following (maybe using a dedicated Maven profile if you wish):

1. Add `http://ericbottard.github.com/cloudfoundry-tomcat-7` as a Maven repository, either through your repo manager of choice or in your pom.xml:

        <repositories>
	    	<repository>
		    	<id>tomcat7-standalone-repo</id>
			    <url>http://ericbottard.github.com/cloudfoundry-tomcat-7</url>
    		</repository>
    	</repositories>

2. Add the following dependency. Use scope provided if you're not using a dedicated profile:

        <dependency>
            <groupId>org.cloudfoundry.samples</groupId>
            <artifactId>tomcat7-standalone</artifactId>
            <version>7.0.29</version>
            <type>tar.gz</type>
        </dependency>

3. Add the following assembly descriptor file in `src/assembly/tomcat7.xml`

        <?xml version="1.0"?>  
        <assembly
	    xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2"
    	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	    xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2 http://maven.apache.org/xsd/assembly-1.1.2.xsd">
    	<id>tomcat7</id>

	    <formats>
		    <format>dir</format>
    	</formats>
	    <baseDirectory>/</baseDirectory>

    	<dependencySets>
	    	<dependencySet>
		    	<outputDirectory>/</outputDirectory>
			    <unpack>true</unpack>
    			<includes>
	    			<include>org.cloudfoundry.samples:tomcat7-standalone:tar.gz</include>
		    	</includes>
    		</dependencySet>
	    </dependencySets>
    	<fileSets>
	    	<fileSet>
		    	<directory>${project.build.directory}/${project.build.finalName}</directory>
    			<outputDirectory>/webapps/ROOT</outputDirectory>
	    	</fileSet>
    	</fileSets>
        </assembly>

4. Configure the assembly plugin to use the file added above:
        
        <plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-assembly-plugin</artifactId>
			<executions>
				<execution>
					<id>package-inside-tomcat7</id>
					<phase>package</phase>
					<goals>
						<goal>attached</goal>
					</goals>
					<configuration>
						<descriptors>
							<descriptor>${basedir}/src/assembly/tomcat7.xml</descriptor>
						</descriptors>
						<appendAssemblyId>false</appendAssemblyId>
						<finalName>tomcat7</finalName>
					</configuration>
				</execution>
			</executions>
		</plugin>

Now, when you build your project, you'll get a tomcat 7 distribution already enabled for CloudFoundry with your app set up as `webapps/ROOT` ready to go. You just have to `vmc push --path target/tomcat7`