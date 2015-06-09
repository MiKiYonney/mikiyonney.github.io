title: Maven in action
date: 2015-06-09 14:34:43
tags:
categories: Java
---

## Maven实战

###Why

1. 我们要开发一个 Java 项目，为了保证编译通过，我们会到处去寻找 jar 包。当编译通过了，在运行的时候，却发现 ClassNotFoundException。还差 jar 包啊？再去找找吧。
2. 每个 Java 项目的目录结构都没有一个统一的标准，配置文件到处都是，单元测试代码到底应该放在哪里，没有一个权威的规范。
3. 可使用 Ant 做为项目构建工具，它可以自动化地完成编译、测试、打包等任务，确实为我们省了不少事儿，但编写 Ant 的 XML 脚本绝非是一件轻松的事情。
<!-- more -->

### What

Maven是一个项目管理工具，它包含了一个项目对象模型 (Project Object Model)，一组标准集合，一个项目生命周期(ProjectLifecycle)，一个依赖管理系统(Dependency Management System)，和用来运行定义在生命周期阶段(phase)中插件(plugin)目标(goal)的逻辑。

统一开发规范与工具+统一管理 jar 包


### How

1. mvn安装
	- M2_HOME = D:/tool/maven
	- MAVEN_OPTS = -Xms128m -Xmx512m

2. mvn安装文件目录
3. mvn命令初体验
	maven archetype:generate 
	mvn clean comilie, mvn clean test,mvn clean package,mvn clean install 
	mvn archetype:create -DgroupId=com.alibaba.maven.plugins -DartifactId=greeting-maven-plugin -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-mojo  
4. mvn项目结构解析—”约定由于配置”、Pom.xml


### Maven深入理解

1. 仓库
2. 坐标和依赖
3. 生命周期和插件
4. 聚合与继承


###1.仓库

###2.1坐标
坐标是Maven构件的唯一标识。坐标由**groupId、artifactId、version、packaging、classifier**构成。

- groupId：组织的实际项目名，建议使用组织的域名反向+项目名称，如org.apache.maven.plugins。
- artifactId：Maven项目（模块）名，建议使用实际项目名作为模块的前缀，如maven-compiler-plugin。
- version：Maven项目的版本。版本号约定为<主版本>.<次版本>.<增量版本>-<里程碑版本>，如1.2.3-beta-1。Maven还定义了SNAPSHOT快照版本，用于持续更新发布。
- packaging：Maven项目的打包方式，通常与生成的构件的扩展名对应，如jar，war。默认为jar。
- classifier：用于帮助定义构建输出的一些附属构件。常见的使用场景如针对不同JDK版本（如jdk5）。

坐标的5个元素中，groupId、artifactId、version是必须指定的，packaging可选（默认为jar），classifier是不能直接定义的（由辅助的插件帮助生成）。

Maven构建出的文件的文件名一般为：**artifactId-version[-classifier].packaging**。

###2.2依赖
依赖的解决实际是Maven在classpath中添加相应的包。Maven项目的依赖由pom.xml中的<dependency>元素设置。dependency元素中常用的子元素包括：

- groupId、artifactId、version：依赖的基本坐标。
- type：依赖的类型，对应于坐标的packaging。一般无需声明，默认为jar。
- scope：依赖的范围。
- optional：是否为可选依赖。
- exclusions：用于排除传递性依赖。


####2.2.1 依赖范围
<img src="http://i.imgur.com/5bJJpAc.png" alt="依赖范围" />

####2.2.2传递性依赖
项目声明依赖构件A，而A声明依赖构件B，则项目对构件B有传递性依赖。项目只需声明直接依赖，Maven会通过传递性依赖机制自动处理间接依赖。

依赖范围对传递性依赖有影响：
<img src="http://i.imgur.com/CmvwMUI.png" alt="传递依赖" />

####2.3依赖调解
依赖调解用于解决依赖的冲突。例如项目依赖了构件A、B，A和B都间接依赖了X，但A依赖了X的1.0版本，B依赖了X的2.0版本，则产生了依赖冲突。

**Maven依赖调解的原则：**

1. 最短路径优先：传递的路径短者优先。如A对X的1.0版本的依赖路径为A -> C -> X（1.0），B对X的2.0版本的依赖路径为B -> D -> E -> X（2.0），由于X 1.0的路径长度（2） < X 2.0的路径长度（3），则最终依赖为X 1.0。
2. 先声明者优先：如果路径相同，则使用声明在前的依赖。

####2.4 可选依赖

如果依赖的optional元素设置为true，则不会产生传递性依赖。如项目A依赖B，B依赖X（可选），则A不依赖于X。

可选依赖的使用场景一般为某个构件提供了多种实现，需根据情况显式选择一种。例如提供了基于Oracle、MySQL等多种持久层实现时，根据采用的数据库选择对应的实现。

####2.5 排除依赖

排除依赖显式的解除某传递性依赖。一般用于替换某传递性依赖的版本。例如A依赖B，B依赖X的版本1.0。可将B对X的依赖通过exclusion元素排除，然后显式指定A依赖X的1.0.1版本（此场景虽然可以通过依赖调解实现，但通过显式指定使用的版本看起来更清晰）。

exclusion元素中只需groupId和artifactId即可，无需version元素。

###3. 生命周期和插件

####3.1 生命周期

Maven为项目的构建过程抽象了统一的生命周期，涵盖了项目的所有构建步骤。

Maven内置了3套相互独立的生命周期：clean、default、site，每个周期又分为多个阶段（phase）。周期的阶段是有顺序的，后边的阶段的执行依赖之前的阶段，即执行后边阶段时Maven会自动依次执行之前的阶段。

####3.1.1 clean生命周期

clean生命周期主要做清理项目的工作。

	pre-clean
	clean
	post-clean

####3.1.2 default生命周期

default生命周期定义了真正构建时需要执行的所有步骤。
	
	validate
	initialize
	generate-sources
	process-sources
	generate-resources
	process-resources
	compile
	process-classes
	generate-test-sources
	generate-test-resources
	process-test-resources
	test-compile
	process-test-classes
	test
	prepare-package
	package
	pre-integration-test
	integration-test
	post-integration-test
	verify
	install
	deploy
####3.1.3 site生命周期

site生命周期用于建立和发布项目站点。

	pre-site
	site
	post-site
	site-deploy

####3.1.4 命令行与生命周期

常用的Maven命令实际调用了Maven的生命周期阶段。如：

- $mvn clean命令：调用clean生命周期的clean阶段（实际执行pre-clean和clean阶段）
- $mvn test命令：调用default生命周期的test阶段（实际执行validate->test的所有阶段）
- $mvn clean install命令：调用clean生命周期的clean阶段 + default生命周期的install阶段
- $mvn clean deploy site-deploy命令：调用clean生命周期的clean阶段 + default生命周期的deploy阶段 + site生命周期的site-deploy阶段

####3.2 插件

Maven的核心仅仅定义了抽象的生命周期，而实际的功能是通过插件实现的。插件以独立的Maven构件的形式存在，在需要时Maven自动从仓库下载。每个Maven的插件实现一类功能，每个功能是一个插件目标（Goal）。

Maven插件的目标需要与指定的生命周期的阶段相绑定，用以完成具体的构建任务。

####3.2.1内置绑定
Maven内置了很多绑定，从而默认可以执行大部分的构建功能：

clean生命周期：

	clean阶段    maven-clean-plugin:clean

default生命周期：

	process-resources阶段    maven-resources-plugin:resources
	compile阶段    maven-compiler-plugin:compile
	process-test-resources阶段    maven-resources-plugin:testResources
	test-compile阶段    maven-compiler-plugin:testCompile
	test阶段    maven-surefire-plugin:test
	package阶段    maven-jar-plugin:jar
	install阶段    maven-install-plugin:install
	deploy阶段    maven-deploy-plugin:deploy

site生命周期：	

	site阶段    maven-site-plugin:site
	site-deploy阶段    maven-site-plugin:deploy

####3.2.2 自定义绑定

除内置绑定外，可以通过plugin元素中的execution子元素自行将某插件目标绑定到生命周期的某个阶段，从而完成自定义功能。

例如下述配置将maven-source-plugin:jar-no-fork目标绑定在package阶段，构建生成源代码包。
```	<plugin>
	    <groupId>org.apache.maven.plugins</groupId>
	    <artifactId>maven-source-plugin</artifactId>
	    <version>2.1.2</version>
	    <executions>
	        <execution>
	            <id>attach-sources</id>
	            <phase>package</phase>
	            <goals>
	                <goal>jar-no-fork</goal>
	            </goals>
	        </execution>
	    </executions>
	</plugin>
```
####3.2.3 从命令行调用插件目标

有些插件目标不适合绑定在生命周期阶段上（例如maven-help-plugin的describe目标），因此Maven支持在命令行调用插件目标。

Maven调用插件目标的格式为：

**$mvn 插件groupId:插件artifactId:插件version:插件goal**

例如：$mvn org.apache.maven.plugins:maven-dependency-plugin:2.1:tree

但插件的坐标比较长，不易记忆，因此Maven引入了插件前缀的概念，例如dependency是maven-dependency-plugin的前缀，上述命令可以简化为$mvn dependency:tree。

注：实际上插件前缀只对应到插件的artifactId。Maven会根据仓库元数据（maven-metadata.xml）信息及内置规则解析到对应的groupId、artifactId、version，从而定位到唯一的插件。

###4. 聚合与继承

####4.1 聚合

较大的Maven项目一般会被拆分成多个模块（例如springframework分为spring-core、spring-context等）。如果每次构建整个项目时都需要依次构建多个模块就显得十分不便。Maven通过聚合模块将一组模块进行统一管理。

聚合模块只有一个pom.xml文件，且坐标中的packaging元素固定为pom。被聚合模块管理的其他模块通过modules元素声明：

	<modules>
	    <module>module-1</module>
	    <module>module-2</module>
	</modules>

聚合模块一般为主从结构，即被聚合的模块作为聚合模块的子目录存储。但此要求不是必须的，也可以将聚合模块与被聚合模块平级存储，这时需要注意修改module元素中的路径：
	
	<modules>
	    <module>../module-1</module>
	    <module>../module-2</module>
	</modules>

定义了聚合模块之后，就可以通过聚合模块对其包含的所有模块统一进行构建操作了。

####4.2 继承

继承的目的主要是消除重复。例如模块A和模块B都包含单元测试，那么两个模块都需要定义junit依赖，造成了重复。可以将junit依赖声明在统一的父模块中，模块A和B都继承自父模块，自身就无需定义junit依赖了。

父模块只有一个pom.xml文件，且坐标中的packaging元素固定为pom。在子模块中通过parent元素声明继承关系：

	<parent>
	    <artifactId>project</artifactId>
	    <groupId>com.company</groupId>
	    <version>1.0.0-SNAPSHOT</version>
	</parent>

需要注意的是，Maven默认父模块和子模块的目录结构为主从关系，即父模块在子模块的上一级目录。如果设定为平行关系，需要在parent元素中通过relativePath元素声明：
	
	<parent>
	    <artifactId>project</artifactId>
	    <groupId>com.company</groupId>
	    <version>1.0.0-SNAPSHOT</version>
	    <relativePath>../parent/pom.xml</relativePath>
	</parent>

Maven的聚合模块和继承模块在概念上是独立的，但在表现形式上很相似。因此实际项目中可以将聚合模块和继承模块合二为一。