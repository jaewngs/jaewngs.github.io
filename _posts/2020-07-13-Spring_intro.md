---



title:  "Spring Intro"



layout: post



---


## [오늘 할 내용]
1. 이클립스에 Spring 환경 구축하기


***

## 스프링 환경세팅(이클립스)
- Help > Eclipse Marketplace... 로 들어가기
- 아래 그림처럼 sts검색 후 두개 설치하기

	![image](https://user-images.githubusercontent.com/52989294/87260855-d4393180-c4ee-11ea-98cd-439f721112d9.png)

***

## 스프링 프로젝트 생성하기
- java 프로젝트로 생성하기
- 프로젝트 우클릭(그림참조)

	![image](https://user-images.githubusercontent.com/52989294/87260766-5b39da00-c4ee-11ea-9aa7-c1fde2c2698b.png)

***

### pom.xml 수정하기
- <https://mvnrepository.com/search?q=spring> 에서 <**dependency**> 복사해서 붙이기

    ~~~ xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>SpringDI_0713</groupId>
        <artifactId>SpringDI_0713</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>4.3.27.RELEASE</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>4.3.27.RELEASE</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>4.3.27.RELEASE</version>
            </dependency>
        </dependencies>
    </project>
    ~~~


































