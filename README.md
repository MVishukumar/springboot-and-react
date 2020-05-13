# Spring Boot backend and ReactJS frontend 

This is simple project that illustrates how to integrate spring boot and ReactJS.

### Goal
  - Keeping frontend and backend in single jar file.
  - See HTML in the right

### Setup
You should have below things installed
  - java
  - maven
  - node  


### Development
  - Create spring boot backend project, we can use the [https://start.spring.io/].
  - Add Controller which responds to GET request with a string.
  - Create ReactJS frontend project in the root directory.
    ```sh
    $ npx create-react-app basic-frontend-app
    $ cd basic-frontend-app
    $ npm start
    ```
  - To fix proxy issues between Spring Boot and ReactJS, follow the instructions given in this page [https://create-react-app.dev/docs/proxying-api-requests-in-development/#configuring-the-proxy-manually]
    ```sh
    $ npm install http-proxy-middleware --save
    ```
  - Next, create src/setupProxy.js and place the following contents in it:
    ```sh
      const { createProxyMiddleware } = require('http-proxy-middleware');
      module.exports = function(app) {
        app.use(
          '/api',
          createProxyMiddleware({
            target: 'http://localhost:8080',
            changeOrigin: true,
          })
        );
      };
    ```
  - Edit App.js to consume service from backend.
  - Create production build of ReactJS project by executing below command.
    ```sh
    $ npm run build
    ```
  - Add below dependency in dependencies section of pom.xml
    ```sh
    <!-- https://mvnrepository.com/artifact/com.github.eirslett/frontend-maven-plugin -->
    <dependency>
        <groupId>com.github.eirslett</groupId>
        <artifactId>frontend-maven-plugin</artifactId>
        <version>1.6</version>
    </dependency>
    ```
  - Add below plugin in plugins section of pom.xml file.
    ```sh
    <plugin>
        <groupId>com.github.eirslett</groupId>
        <artifactId>frontend-maven-plugin</artifactId>
        <version>1.6</version>
        <configuration>
            <workingDirectory>basic-frontend-app</workingDirectory>
            <installDirectory>target</installDirectory>
        </configuration>
        <executions>
            <execution>
                <id>install node and npm</id>
                <goals>
                    <goal>install-node-and-npm</goal>
                </goals>
                <configuration>
                    <nodeVersion>v8.9.4</nodeVersion>
                    <npmVersion>5.6.0</npmVersion>
                </configuration>
            </execution>
            <execution>
                <id>npm install</id>
                <goals>
                    <goal>npm</goal>
                </goals>
                <configuration>
                    <arguments>install</arguments>
                </configuration>
            </execution>
            <execution>
                <id>npm run build</id>
                <goals>
                    <goal>npm</goal>
                </goals>
                <configuration>
                    <arguments>run build</arguments>
                </configuration>
            </execution>
        </executions>
    </plugin>
    <plugin>
        <artifactId>maven-antrun-plugin</artifactId>
        <executions>
            <execution>
                <phase>generate-resources</phase>
                <configuration>
                    <target>
                        <copy todir="${project.build.directory}/classes/public">
                            <fileset dir="${project.basedir}/basic-frontend-app/build"/>
                        </copy>
                    </target>
                </configuration>
                <goals>
                    <goal>run</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <executions>
            <execution>
                <phase>package</phase>
                <goals><goal>copy</goal></goals>
                <configuration>
                    <artifactItems>
                        <artifactItem>
                            <groupId>com.heroku</groupId>
                            <artifactId>webapp-runner</artifactId>
                            <version>9.0.30.0</version>
                            <destFileName>webapp-runner.jar</destFileName>
                        </artifactItem>
                    </artifactItems>
                </configuration>
            </execution>
        </executions>
    </plugin>
    ```
  - Build maven project
    ```sh
    mvn clean install
    ```
  - Verify /target contains both frontend files and java files.