title: 'springboot-maven-plugin '
author: Charles Cliff
date: 2018-03-28 10:28:55
tags:
---
springboot+maven成为目前java web项目开发的普遍启动方式，相比于以前的springframework+maven方式开发人员的配置效率已经得到了极大的提高，学习成本也比低不少（当然都会是最好的）。最近的项目也采用了前一种配置方式，其中使用了一个maven插件[springboot-maven-plugin](https://docs.spring.io/spring-boot/docs/current/maven-plugin/usage.html)。由于项目采用了多module方式，module之前存在着依赖，在原始的maven配置(maven-compile-plugin)下编译没有问题，但是在配置了springboot-maven-plugin的时候模块之间的依赖总是找不到.class文件。折腾后发现，问题出在springboot-maven-plugin插件的repackage。

# 项目结构
maven配置下的多模块接口
* module P , parent module, 父模块
* module A, 基础模块
* module B, 业务模块（Application)，依赖于A


# spring-boot:repackage
repackage 是这个插件最重要的功能，这个过程主要是按照配置对maven编译好的包进行二次打包，以使得打包好的JAR/WAR包能使用java -jar ${JAR_ARCHIVE}直接运行而无需配置main-class。这个插件的默认配置很让人迷惑，repackage后的包在默认配置下会使得该模块不可被其他模块依赖（依赖它的模块找不到.class文件）， 原因是repackge默认把当前项目当做是`Springbot Application`，会寻找其main-class 并把编译后的文件（classes lib目录）移动到BOOT-INF文件夹下，导致maven找不到依赖的.class。下面介绍几种可行的配置方案。

1. 不使用springboot-maven-plugin  

  这是最简单直接的方式，当然执行`java -jar ${JAR_ARCHIVE}`的时候需要指定Main-Class

2. parent module 配置插件，非application module 额外配置

  parent module 配置了该插件后，所有模块编译的时候都会被默认为是application, 只需在不想作为application的模块中配置该模块不作为可执行包来打包即可. 这种方式在作为基础包依赖包的模块较少而application模块较多的时候能减少配置。
 ``` xml
  <!-- parent module-->
  <plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
      <execution>
        <goals>
          <goal>repackage</goal>
        </goals>
      </execution>
    </executions>
  </plugin>
 ```

  ``` xml
<!-- basic module 配置classifier， 这样会生成两个包， maven原包（.original结尾）和repackage后的包 -->
<plugin>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-maven-plugin</artifactId>
  <executions>
    <execution>
      <goals>
        <goal>repackage</goal>
      </goals>
      <configuration>
        <classifier>exec</classifier>
      </configuration>
    </execution>
  </executions>
</plugin>

  ```

3. parent module不配置， application module配置
这种方式在只在需要作为springboot application的包中配置插件，如果你有多个module都要作为被依赖包而不需要启动application, 那么会比较省配置。当然如果一个模块即作为application又需要被其他模块依赖，那么采用**2**中的`classifier=exec`配置方式比较合理


使用哪种方式配置插件，需要根据项目需要进行选择。这个坑很容易踩，因为这个插件帮我们做了一些事情，但是却少有人认真的读文档。插件的目的是为了简化开发和配置，同时也带来了学习成本和盲区。因此一定要注意配置的默认值，很多问题都出在选择了默认配置，却不清楚默认的配置做了些什么。