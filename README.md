# CloudFoundry Tomcat 7
A modified version of Tomcat v7.0.29 to run on [CloudFoundry](http://www.cloudfoundry.com), as a standalone app, until Tomcat7 is available as a runtime.

Bascially, this is just what is described [here](http://blog.cloudfoundry.org/2012/06/18/deploying-tomcat-7-using-the-standalone-framework/) all done for you.

## Usage
Just

	$git clone git@github.com:ericbottard/cloudfoundry-tomcat-7.git
	$cd cloudfoundry-tomcat-7
	<Replace webapps/ROOT with your own app>
	$vmc push
	
