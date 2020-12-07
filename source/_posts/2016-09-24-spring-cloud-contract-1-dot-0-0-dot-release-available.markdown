---
layout: post
title: "Spring Cloud Contract 1.0.0.RELEASE available!"
date: 2016-09-24 20:39:25 +0200
comments: true
categories: articles
tags:
- spring-cloud
- deployment
- accurest
- spring-cloud-contract
- consumer-driven-contracts
- cdc
---

I've just published an article at the Spring blog about [Spring Cloud Contract 1.0.0.RELEASE is available](
https://spring.io/blog/2016/09/23/spring-cloud-contract-1-0-0-release-is-available).

I'm really happy that the project is GA. Even though as the Accurest project we had already done a GA release, it really feels that a lot of effort was put in order to release the GA version under the Pivotal's Spring Cloud branding. Let's look at some numbers:

- first commit almost 2 years ago: [2014-12-06 18:20:29 by Jakub Kubrynski](https://github.com/spring-cloud/spring-cloud-contract/commit/dfaddaa98d645b818ba3947c9267ef7ac8ed9ba4) - thanks to [Codearte](https://codearte.eu) the authors of [DevSkiller](https://devskiller.com) for their support!!!
- [1.152 commits](https://github.com/spring-cloud/spring-cloud-contract/commits/master)
- [20 contributors](https://github.com/spring-cloud/spring-cloud-contract/graphs/contributors)

That's quite a lot of work! But there we are, with a library that has already been battle-proven on production by many companies, even before being GA as Spring Cloud Contract.

<!-- more -->

## What's new in comparison to Accurest?

Like I mentioned, Accurest was already GA. So what are the main difference apart from rebranding and bug fixes?

- we've moved from Grapes to Aether to download stubs
- [we generate fake data when you provide either consumer or producer in the DSL](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_what_is_this_value_consumer_producer)
- [Consumer Contract approach is there](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_common_repo_with_contracts)
- Spring Cloud Contract is available on [start.spring.io](https://start.spring.io)
- [you can have more than one base class for your tests](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_different_base_classes_for_contracts_2)
- [Spring Cloud Stub Runner Boot can register stubs in Eureka / Consul / Zookeeper using Spring Cloud](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_additional_configuration)
- the whole build was moved from Gradle to the standard Spring Cloud Maven setup

These are the Spring Cloud Contract Verifier changes. Apart from that Spring Cloud Contract consists of [Spring Cloud Contract WireMock support](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_spring_cloud_contract_wiremock) and [Spring Cloud Contract RestDocs](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_generating_stubs_using_restdocs). Thanks to the first one the integration with [WireMock](https://wiremock.org) is much more efficient and thanks to the latter you don't have to use the Groovy DSL - you can define your stubs by yourself by attaching them to an existing RestDocs test.

As far as Spring Cloud Contract Verifier is concerned the biggest two changes are the [Consumer Contract support](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_common_repo_with_contracts) and that [you can have more than one base class for your tests](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_different_base_classes_for_contracts_2). Let's take a closer look what's there in the docs about them...

### Consumer Contract support

Another way of storing contracts other than having them with the producer is keeping them in a common place. It can be related to security issues where the consumers can’t clone the producer’s code. Also if you keep contracts in a single place then you, as a producer, will know how many consumers you have and which consumer will you break with your local changes.

#### Repo structure

Let’s assume that we have a producer with coordinates com.example:server and 3 consumers: client1, client2, client3. Then in the repository with common contracts you would have the following setup (which you can checkout here:

```
├── com
│   └── example
│       └── server
│           ├── client1
│           │   └── expectation.groovy
│           ├── client2
│           │   └── expectation.groovy
│           ├── client3
│           │   └── expectation.groovy
│           └── pom.xml
├── mvnw
├── mvnw.cmd
├── pom.xml
└── src
    └── assembly
        └── contracts.xml
```
As you can see the under the slash-delimited groupid / artifact id folder (`com/example/server`) you have expectations of the 3 consumers (`client1`, `client2` and `client3`). Expectations are the standard Groovy DSL contract files as described throughout this documentation. This repository has to produce a JAR file that maps one to one to the contents of the repo.

Example of a `pom.xml` inside the `server` folder.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>server</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<name>Server Stubs</name>
	<description>POM used to install locally stubs for consumer side</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.4.0.BUILD-SNAPSHOT</version>
		<relativePath />
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.8</java.version>
		<spring-cloud-contract.version>1.0.1.BUILD-SNAPSHOT</spring-cloud-contract.version>
		<spring-cloud-dependencies.version>Camden.BUILD-SNAPSHOT</spring-cloud-dependencies.version>
	</properties>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud-dependencies.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-contract-maven-plugin</artifactId>
				<version>${spring-cloud-contract.version}</version>
				<extensions>true</extensions>
				<configuration>
					<!-- By default it would search under src/test/resources/ -->
					<contractsDirectory>${project.basedir}</contractsDirectory>
				</configuration>
			</plugin>
		</plugins>
	</build>

	<repositories>
		<repository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</repository>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
		<repository>
			<id>spring-releases</id>
			<name>Spring Releases</name>
			<url>https://repo.spring.io/release</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</repository>
	</repositories>
	<pluginRepositories>
		<pluginRepository>
			<id>spring-snapshots</id>
			<name>Spring Snapshots</name>
			<url>https://repo.spring.io/snapshot</url>
			<snapshots>
				<enabled>true</enabled>
			</snapshots>
		</pluginRepository>
		<pluginRepository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
		<pluginRepository>
			<id>spring-releases</id>
			<name>Spring Releases</name>
			<url>https://repo.spring.io/release</url>
			<snapshots>
				<enabled>false</enabled>
			</snapshots>
		</pluginRepository>
	</pluginRepositories>

</project>
```

As you can see there are no dependencies other than the Spring Cloud Contract Verifier Maven plugin. Those poms are necessary for the consumer side to run `mvn clean install -DskipTests` to locally install stubs of the producer project.

The `pom.xml` in the root folder can look like this:

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
		 xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example.standalone</groupId>
	<artifactId>contracts</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<name>Contracts</name>
	<description>Contains all the Spring Cloud Contracts, well, contracts. JAR used by the producers to generate tests and stubs</description>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	<build>
		<plugins>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-assembly-plugin</artifactId>
				<executions>
					<execution>
						<id>contracts</id>
						<phase>prepare-package</phase>
						<goals>
							<goal>single</goal>
						</goals>
						<configuration>
							<attach>true</attach>
							<descriptor>${basedir}/src/assembly/contracts.xml</descriptor>
							<!-- If you want an explicit classifier remove the following line -->
							<appendAssemblyId>false</appendAssemblyId>
						</configuration>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>

</project>
```

It’s using the assembly plugin in order to build the JAR with all the contracts. Example of such setup is here:

```
<assembly xmlns="https://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.3"
		  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
		  xsi:schemaLocation="https://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.3 https://maven.apache.org/xsd/assembly-1.1.3.xsd">
	<id>project</id>
	<formats>
		<format>jar</format>
	</formats>
	<includeBaseDirectory>false</includeBaseDirectory>
	<fileSets>
		<fileSet>
			<directory>${project.basedir}</directory>
			<outputDirectory>/</outputDirectory>
			<useDefaultExcludes>true</useDefaultExcludes>
			<excludes>
				<exclude>**/${project.build.directory}/**</exclude>
				<exclude>mvnw</exclude>
				<exclude>mvnw.cmd</exclude>
				<exclude>.mvn/**</exclude>
				<exclude>src/**</exclude>
			</excludes>
		</fileSet>
	</fileSets>
</assembly>
```

#### Workflow

The workflow would look similar to the one presented in the [Step by step guide to CDC](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_step_by_step_guide_to_cdc). The only difference is that the producer doesn’t own the contracts anymore. So the consumer and the producer have to work on common contracts in a common repository.

#### Consumer

When the consumer wants to work on the contracts offline, instead of cloning the producer code, the consumer team clones the common repository, goes to the required producer’s folder (e.g. `com/example/server`) and runs `mvn clean install -DskipTests` to install locally the stubs converted from the contracts.

**REMEMBER! You need to have Maven installed locally**

#### Producer

As a producer it’s enough to alter the Spring Cloud Contract Verifier to provide the URL and the dependency of the JAR containing the contracts:

```
<plugin>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-contract-maven-plugin</artifactId>
	<configuration>
		<contractsRepositoryUrl>https://link/to/your/nexus/or/artifactory/or/sth</contractsRepositoryUrl>
		<contractDependency>
			<groupId>com.example.standalone</groupId>
			<artifactId>contracts</artifactId>
		</contractDependency>
	</configuration>
</plugin>
```

With this setup the JAR with groupid `com.example.standalone` and artifactid contracts will be downloaded from `https://link/to/your/nexus/or/artifactory/or/sth`. It will be then unpacked in a local temporary folder and contracts present under the `com/example/server` will be picked as the ones used to generate the tests and the stubs. Due to this convention the producer team will know which consumer teams will be broken when some incompatible changes are done.

The rest of the flow looks the same.

### More than one base class

That was quite a problem when providing one single base class for all the tests. After some time the mock configurations were enormous! That's why we've added a possibility to map a contract to its test base class.

#### Gradle

If your base classes differ between contracts you can tell the Spring Cloud Contract plugin which class should get extended by the autogenerated tests. You have two options:

- follow a convention by providing the `packageWithBaseClasses`
- provide explicit mapping via `baseClassMappings`

##### Convention

The convention is such that if you have a contract under e.g. `src/test/resources/contract/foo/bar/baz/` and provide the value of the `packageWithBaseClasses` property to `com.example.base` then we will assume that there is a `BarBazBase` class under `com.example.base` package. In other words we take last two parts of package if they exist and form a class with a `Base` suffix. Takes precedence over `baseClassForTests`. Example of usage in the contracts closure:

```
packageWithBaseClasses = 'com.example.base'
```

##### Mapping

You can manually map a regular expression of the contract’s *package* (package, not folder) to fully qualified name of the base class for the matched contract. Let’s take a look at the following example:

```
baseClassForTests = "com.example.FooBase"
baseClassMappings {
	baseClassMapping('.*com.*', 'com.example.ComBase')
	baseClassMapping('.*bar.*':'com.example.BarBase')
}
```

Let’s assume that you have contracts under

- `src/test/resources/contract/com/`
- `src/test/resources/contract/foo/`

By providing the `baseClassForTests` we have a fallback in case mapping didn’t succeed (you could also provide the `packageWithBaseClasses` as fallback). That way the tests generated from `src/test/resources/contract/com/` contracts will be extending the `com.example.ComBase` whereas the rest of tests will extend `com.example.FooBase` cause they don't match the base class mapping for `bar` folder.

#### Maven

Let's now look how it looks like for Maven.

##### Convention

To accomplish the same result as the one presented for Gradle you'd have to set your configuration like this:

```
<plugin>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-contract-maven-plugin</artifactId>
	<configuration>
		<packageWithBaseClasses>com.example.base</packageWithBaseClasses>
	</configuration>
</plugin>
```

##### Mapping

You can manually map a regular expression of the contract’s package to fully qualified name of the base class for the matched contract. You have to provide a list `baseClassMappings` of `baseClassMapping` that takes a `contractPackageRegex` to `baseClassFQN` mapping. Let’s take a look at the following example:

```
<plugin>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-contract-maven-plugin</artifactId>
	<configuration>
		<baseClassForTests>com.example.FooBase</baseClassForTests>
		<baseClassMappings>
			<baseClassMapping>
				<contractPackageRegex>.*com.*</contractPackageRegex>
				<baseClassFQN>com.example.ComBase</baseClassFQN>
			</baseClassMapping>
  			<baseClassMapping>
  				<contractPackageRegex>.*bar.*</contractPackageRegex>
  				<baseClassFQN>com.example.BarBase</baseClassFQN>
  			</baseClassMapping>
		</baseClassMappings>
	</configuration>
</plugin>
```

## Summary

In this blog post we've checked what are the new and shiny features in the GA of Spring Cloud Contract. We've also checked some history around Accurest to Spring Cloud Contract migration.

## Links

Here you can find interesting links related to Spring Cloud Contract Verifier:

- [Spring Cloud Contract Github Repository](https://github.com/spring-cloud/spring-cloud-contract/)
- [Spring Cloud Contract Main project page](https://cloud.spring.io/spring-cloud-contract/)
- [Spring Cloud Contract Documentation](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html)
- [Spring Cloud Contract Stub Runner Documentation](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract.html#_spring_cloud_contract_stub_runner)
- [Spring Cloud Contract Gitter](https://gitter.im/spring-cloud/spring-cloud-contract)
- [Spring Cloud Contract Maven Plugin Documentation](https://cloud.spring.io/spring-cloud-contract/spring-cloud-contract-maven-plugin/)
