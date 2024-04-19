## Hi there 👋

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->

# Development Setup on Eclipse
- put settings.xml as follow to your .m2 directory.
```
<settings>
    <servers>
        <server>
            <id>github</id>
            <username>user_name</username>
            <password>ghp_token</password>
        </server>
    </servers>
</settings>
```
- clone repositories to touch.
- right click the project -> Maven -> update project

# Library Dependencies
```
owl-dev(9.0-SNAPSHOT)
<--- owl-utils(6.0-SNAPSHOT), owl-utils-poi(2.0-SNAPSHOT)
<--- owl-utils-dba(2.0-SNAPSHOT)
<--- kangaroo(6.1-SNAPSHOT)
```
## Web Application
```
kangaroo(6.1-SNAPSHOT)
<-- app(1.0-SNAPSHOT)
```
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>biz.xpenguin.owl</groupId>
		<artifactId>owl-dev</artifactId>
		<version>9.0-SNAPSHOT</version>
	</parent>
	<groupId>jp.tokyo.foreach</groupId>
	<artifactId>app</artifactId>
	<version>1.0-SNAPSHOT</version>
	<packaging>war</packaging>
	<name>app</name>
	<url>https://github.com/4each-biz/app</url>
	<repositories>
		<repository>
			<id>github</id>
			<name>GitHub Apache Maven Packages</name>
			<url>https://maven.pkg.github.com/4each-biz/registry</url>
			<snapshots>
				<enabled>true</enabled>
				<updatePolicy>always</updatePolicy>
			</snapshots>
		</repository>
	</repositories>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>
	<dependencies>
		<dependency>
			<groupId>biz.xpenguin.kangaroo</groupId>
			<artifactId>kangaroo</artifactId>
			<version>6.1-SNAPSHOT</version>
			<type>jar</type>
			<scope>provided</scope>
		</dependency>
	</dependencies>
</project>
```

# Workflow(Actions)
## CI(on pull_request)
```
name: Java CI with Maven
on:
  pull_request:
    branches: [ "main" ]
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        java-version: '17'
        distribution: 'temurin'
        server-id: github
        server-username: REPOSITORY_SERVER_USER
        server-password: REPOSITORY_SERVER_PASSWORD
        settings-path: ${{ github.workspace }}
        cache: maven
    - name: Build with Maven
      run: mvn -B package --file pom.xml -s $GITHUB_WORKSPACE/settings.xml
      env:
        REPOSITORY_SERVER_USER: ${{ secrets.REPOSITORY_SERVER_USER }}
        REPOSITORY_SERVER_PASSWORD: ${{ secrets.REPOSITORY_SERVER_PASSWORD }}
```
## deploy package to registry (on push to main)
```
name: Maven Package
on:
  push:
    branches: [ "main" ]
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'
        server-id: github # Value of the distributionManagement/repository/id field of the pom.xml
        server-username: REPOSITORY_SERVER_USER
        server-password: REPOSITORY_SERVER_PASSWORD
        settings-path: ${{ github.workspace }} # location for the settings.xml file
    - name: Build with Maven
      run: mvn -B deploy --file pom.xml -s $GITHUB_WORKSPACE/settings.xml
      env:
        REPOSITORY_SERVER_USER: ${{ secrets.REPOSITORY_SERVER_USER }}
        REPOSITORY_SERVER_PASSWORD: ${{ secrets.REPOSITORY_SERVER_PASSWORD }}
```

## Settings on each repository
```
Settings > Secrets and variables > Actions
REPOSITORY_SERVER_USER : maven202312
REPOSITORY_SERVER_PASSWORD : ask ytx
```
