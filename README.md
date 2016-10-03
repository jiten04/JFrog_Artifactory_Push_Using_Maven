# Publish to JFrog Artifactory with Maven in Shippable CI

### 1. Adding the Account Integration
You will need to configure this integration to store credentials for artifactory.

1. Click on the gear icon for Account Settings in your top navigation bar and then click on the 'Integrations' section.
2. Click on the Add Integration button.
3. For 'Integration type', choose JFrog Artifactory from the list of dropdown choices.
4. For 'Integration Name' use a distinctive name that's easy to associate to the integration and recall. Example: jfrog
5. Enter your HTTP Endpoint(url), username, password. Click on Save.

> The integration will now be available to all your continuous integration.

![JFrog_Integration](https://github.com/amalfra/JFrog_Artifactory_Push_Using_Maven/blob/master/.images/account_integration.png)

### 2. Adding the JFrog Artifactory account integration to your subscription
To add JFrog Artifactory integration to your subscription, do the following:

1. Select your Subscription from the dropdown burger bar menu on the top left.
2. Click the 'Settings' tab and go to the 'Integrations' section.
3. Click the Add Integration button.
4. Provide an easy-to-remember name for the Jfrog Artifactory integration for your Subscription, such as `jfrog`, in the 'Name' field. IMPORTANT: The 'Name' you have entered in this step should be used in your `shippable.yml` file. Both names should be exactly the same. If not the build will fail with an error.
5. From the 'Account Integrations' dropdown select the JFrog Artifactory account integration created.
6. Click the `Save` button.
7. The JFrog Artifactory integration will show up in the list of integrations for your subscription.

![JFrog_SubInt](https://github.com/amalfra/JFrog_Artifactory_Push_Using_Maven/blob/master/.images/subscription_integration.png)

![List_SubInts](https://github.com/amalfra/JFrog_Artifactory_Push_Using_Maven/blob/master/.images/list_subscription_integration.png)

Here's an example , for reference to configure JFrog Artifactory integration in `shippable.yml`.

```yml
integrations:
  hub:
    - integrationName: jfrog
      type: artifactory
```
### 3. Configuring Maven project's pom.xml
This example will use the [maven artifactory plugin](https://www.jfrog.com/confluence/display/RTF/Maven+Artifactory+Plugin). You will need to edit contextUrl, username, password of plugin configuration. The  HTTP Endpoint(url), username, password you gave for account integration in step 1 will be available in build enviornment variables `ARTIFACTORY_URL`, `ARTIFACTORY_USERNAME` and `ARTIFACTORY_PASSWORD`. Maven artifactory plugin supports enviornment variables and hence mentioned envs can be used in your plugin configration.

Here's an example as of maven artifactory plugin configuration:
```xml
<plugin>
	<groupId>org.jfrog.buildinfo</groupId>
	<artifactId>artifactory-maven-plugin</artifactId>
	<version>2.4.1</version>
	<inherited>false</inherited>
	<executions>
	    <execution>
		<id>build-info</id>
		<goals>
		    <goal>publish</goal>
		</goals>
		<configuration>
		    <deployProperties>
		        <gradle>awesome</gradle>
		    </deployProperties>
		    <artifactory>
		        <includeEnvVars>true</includeEnvVars>
		        <timeoutSec>60</timeoutSec>
		        <propertiesFile>publish.properties</propertiesFile>
		    </artifactory>
		    <publisher>
		        <contextUrl>{{ARTIFACTORY_URL}}</contextUrl>
		        <username>{{ARTIFACTORY_USERNAME}}</username>
		        <password>{{ARTIFACTORY_PASSWORD}}</password>
		        <excludePatterns>*-tests.jar</excludePatterns>
		        <repoKey>libs-release-local</repoKey>
		        <snapshotRepoKey>libs-snapshot-local</snapshotRepoKey>
		    </publisher>
		    <buildInfo>
		        <buildName>plugin-demo</buildName>
		        <buildNumber>1</buildNumber>
		        <buildUrl>http://build-url.org</buildUrl>
		    </buildInfo>
		    <licenses>
		        <autoDiscover>true</autoDiscover>
		        <includePublishedArtifacts>false</includePublishedArtifacts>
		        <runChecks>true</runChecks>
		        <scopes>compile,runtime</scopes>
		        <violationRecipients>build@organisation.com</violationRecipients>
		    </licenses>
		</configuration>
	    </execution>
	</executions>
</plugin>
```
### 4. Pushing to JFrog Artifactory
Now you can initiate push into your JFrog Artifactory with ```mvn deploy``` in build ci section of `shippable.yml`.

Here's an example of configuring your yml to run command during build:
```yml
build:
  ci:
    - mvn deploy
```
