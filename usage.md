## 目录

* 简介
* 原理
* 依赖
* 使用
* docker

## 简介

为API开发者设计的管理平台，YApi让接口开发更简单高效，让接口的管理更具可读性、可维护性，让团队协作更合理。[YPpi](http://yapi.demo.qunar.com/)

## 原理

YPpi官方文档指定的数据导入方式主要有以下几种 

1. Postman数据导入
2. HAR数据导入
3. Swagger数据导入
4. JSON数据导入
5. 通过命令行导入

而使用方式主要有两种，第一中是通过界面进行导入已有的文件
![图片](https://github.com/FeiMin/springmvc-swagger-yapi/blob/master/img/4.png)
第二种是通过命令导入方式
![图片2](https://github.com/FeiMin/springmvc-swagger-yapi/blob/master/img/5.png)
两种方式均可使用，第一种方式比较适合约定接口后本地生成文件直接上传生成接口信息（***注意此处，页面上传方式存在中文乱码情况，本人使用官方提供的数据测试得出的结果***）。第二种方式不存在中文乱码情况，而且适合自动化部署。

说完YPpi支持的接入后，下一步我们就是要考虑如何生成接口的描述文档。因之前有在使用的swagger作为接口的描述工具，而且恰巧YPpi也支持swagger的导入，自然把swagger文档作为接口文档来生成。在查找相关资料后选择[Yukai Kong](https://github.com/kongchen)的[swagger-maven-plugin](https://github.com/kongchen/swagger-maven-plugin)插件用于生成swagger文档。

## 依赖

此次我们选择试用swagger-maven-plugin(3.1.4版本)，根据swagger-maven-plugin的试用说明，主要有以下依赖

swagger-core

```
<dependency>
    <groupId>io.swagger</groupId>
    <artifactId>swagger-core</artifactId>
    <version>1.5.16</version>
    <scope>compile</scope>
        <exclusions>
        <exclusion>
            <groupId>javax.ws.rs</groupId>
             <artifactId>jsr311-api</artifactId>
         </exclusion>
     </exclusions>
</dependency>
```

swagger-maven-plugin

```
<plugin>
    <groupId>com.github.kongchen</groupId>
    <artifactId>swagger-maven-plugin</artifactId>
    <version>3.1.4</version>
    <configuration>
        <apiSources>
            <apiSource>
             <!-- spring mvc支持 -->
                <springmvc>true</springmvc>
                <!-- restControler包名 -->
                <locations>
                    <location>com.antfact.learn.rest</location>
                </locations>
                <!-- 请求方式 -->
                <schemes>
                    <scheme>http</scheme>
                    <scheme>https</scheme>
                </schemes>
                <!-- 主机名 -->
                <host>xxxx</host>
                <!-- contentPath -->
                <basePath>/api</basePath>
                <info>
                    <title>Swagger Maven Plugin Sample</title>
                    <version>v1</version>
                    <description>
                        This is a sample.
                    </description>
                    <termsOfService>
                        http://www.github.com/kongchen/swagger-maven-plugin
                    </termsOfService>
                    <contact>
                        <email>xxx</email>
                        <name>xxx</name>
                        <url>xxx</url>
                    </contact>
                    <license>
                        <url>http://www.apache.org/licenses/LICENSE-2.0.html</url>
                        <name>Apache 2.0</name>
                    </license>
                </info>
                <!-- swagger文档生成位置 -->
                <swaggerDirectory>${basedir}/src/main/generated/swagger-ui</swaggerDirectory>
            </apiSource>
        </apiSources>
    </configuration>
    <executions>
        <execution>
            <phase>compile</phase>
            <goals>
                <goal>generate</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## 使用

使用比较简单，依赖加完后执行mvn clean compile即可在配置的目录中生成接口的swagger文档，接下来就可以按YPpi支持的方式进行导入。根据本人的实践经验，说下遇到的坑

* swagger-maven-plugin插件生成Springmvc接口文档时需要在controller上有@Api标识，接口路径必须以@RequestMapping描述。
* YPpi的页面上传swagger文件会有中文乱码
接口上传后可以在YPpi中进行编辑、mock、和运行了。如下为使用示例
![tu](https://github.com/FeiMin/springmvc-swagger-yapi/blob/master/img/1.png)
mock
![tu](https://github.com/FeiMin/springmvc-swagger-yapi/blob/master/img/2.png)
测试
![tu](https://github.com/FeiMin/springmvc-swagger-yapi/blob/master/img/3.png)
## Docker

Openshift部署可以通过执行compile生成swagger文档，然后通过命令行导入的方式将接口同步到YPpi

