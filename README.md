# Maven Hello World Starter

A template to start a new Maven project from.

## Exercise

Please follow the instructions to get started with the Maven Exercise. In this exercise we will build a Java CLI
application
that prints greets the user in different languages and outputs the text in different colors.
We will display use Maven to build the application, manage resources and manage dependencies.

### 1. Download the repository

1. Download this repository's zip file and extract it to a folder on your computer.
2. Open the folder in IntelliJ IDEA.
3. Run the org.example.Main class to verify that the application runs. It should print `Hello World!` to the console.
4. Initialize a new Git repository in the folder by running `git init` in the terminal.
5. Use `git add .` to add all files to the staging area.
6. Use `git commit -m "Initial commit"` to commit the files to the repository.
7. Create a new repository on GitHub and follow the instructions on Github to link it to your local repository.
8. Push your local repository to the remote repository on Github.
9. Verify that your files are on Github.

#### 1.1 Build the application

To build the application we need to run the `build` task.
Run the following command in the terminal to build the application:

```shell script
./mvnw compile
```

#### 1.2 Run the application

We can run the application by running the `org.example.Main` class:

```shell script
cd target/classes
```

```shell script
java org.example.Main
```

This should print out `Hello World!` to the console.

### 2. Develop & Build the Hello Maven application

First, we need to see how we can color the text that we print to the console.
We will use the [ANSI escape codes](https://en.wikipedia.org/wiki/ANSI_escape_code) to color the text.
In Java we can use [Jansi](https://github.com/fusesource/jansi) library to print colored text to the console.

#### 2.1. Add Jansi as a dependency

To add the Jansi library as a dependency we need to add the following lines to the `pom.xml` file:
To find the Jansi library on [Maven Central](https://search.maven.org/) we can search for `jansi`.
The results will get you to this [link](https://central.sonatype.com/artifact/org.fusesource.jansi/jansi/2.4.0), from
where you can also copy the dependency declaration:

```xml

<dependencies>
    <dependency>
        <groupId>org.fusesource.jansi</groupId>
        <artifactId>jansi</artifactId>
        <version>2.4.0</version>
    </dependency>
</dependencies>
```

#### 2.2. Use Jansi to print colored text

To use Jansi we need to import the `org.fusesource.jansi.AnsiConsole` class and call the `systemInstall()` method.
This will install the Jansi library and allow us to use the `Ansi` class to print colored text to the console.
In the org.example.Main class, add the following lines to the `main` method:

```java
package org.example;

import org.fusesource.jansi.Ansi;
import org.fusesource.jansi.AnsiConsole;

public class Main {

    public static void main(String[] args) {
        AnsiConsole.systemInstall();
        System.out.println(Ansi.ansi().fg(Ansi.Color.RED).a("Hello World!").reset());
        AnsiConsole.systemUninstall();
    }
}
```

This will print the text `Hello World!` in red to the console.
However, running the code with IntelliJ will not print the text in red. We need to run it from the terminal.
To run the application from the terminal, we need to build it first. We will use Maven to build the application.
Follow the instructions in the next section to build the application.

#### 2.3. Build the application

To build the application we need to run the `build` task.
Run the following command in the terminal to build the application:

```shell script
./mvnw clean package
```

This will create a `target` folder in the project's root folder. The `target` folder will contain the compiled classes
and the JAR file.
The JAR file will be located directly in the folder. The name of the JAR file will be `hello-maven-0.1.0-SNAPSHOT.jar`.
The `target/classes` folder will contain the compiled classes. The `org.example.Main` class will be located in
the `org/example` folder.

#### 2.4 Run the application

We can run the application in multiple different ways:

##### 2.4.1 Running the Main class

We can run the application by running the `org.example.Main` class:

```shell script
cd target/classes
```

```shell script
java org.example.Main
```

This will however produce an error message:

```shell script
Error: ClassNotFoundException org.fusesource.jansi.AnsiConsole not found
```

This is because the `Jansi` library is not on the classpath. We can fix this by adding the required JAR files to the
classpath, which we will do in the next section

##### 2.4.2 We can run the application by running the JAR file directly:

```shell script
java -jar target/hello-maven-0.1.0-SNAPSHOT.jar
```

However, this will display an error message:

```shell script
no main manifest attribute, in target/hello-maven-0.1.0-SNAPSHOT.jar
```

This is because the JAR file does not contain the `Main-Class` attribute in the `MANIFEST.MF` file.
We can fix this by adding the `Main-Class` attribute to the `MANIFEST.MF` file, which we do by adding the following
lines in the `pom.xml` file:

```xml

<build>
    <plugins>
        <plugin>
            <artifactId>maven-assembly-plugin</artifactId>
            <configuration>
                <finalName>hello-maven</finalName>
                <archive>
                    <manifest>
                        <mainClass>org.example.Main</mainClass>
                    </manifest>
                </archive>
                <descriptorRefs>
                    <descriptorRef>jar-with-dependencies</descriptorRef>
                </descriptorRefs>
                <appendAssemblyId>false</appendAssemblyId>
            </configuration>
            <executions>
                <execution>
                    <id>make-assembly</id>
                    <phase>package</phase>
                    <goals>
                        <goal>single</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

This will create a `fat` JAR file, which contains all the dependencies (including the `org.fusesource.jansi:jansi:2.4.0` jar).
This also adds the `org.example.Main` class as the entry point of the application (the `Main-Class` attribute in the `MANIFEST.MF` file).
The file will be located at `target/hello-maven.jar`.

Try running the application again:

```shell script
java -jar target/hello-maven.jar
```

This will print `Hello World!` to the console (in red).

##### 2.4.3 Running the application with Maven `exec:java` goal

We can also run the application by using the `java` goal of the `exec` plugin:

```shell script
mvn exec:java -Dexec.mainClass="com.example.App"
```

This will however fail, because the `exec` plugin does not exist in the context of the `pom.xml` file.
We can fix this by adding the following plugin to the `pom.xml` file:

```xml

<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.6.0</version>
    <configuration>
        <mainClass>org.example.Main</mainClass>
    </configuration>
</plugin>
```

By adding the `exec` plugin, we are able to use the `java` goal. We are also configuring the `exec:java` task to use
the `org.example.Main` class as the main class of the application.

Now, when we run the `./mvnw exec:java` command, the application will be run.

This will print `Hello World!` to the console (in red).

##### 2.4.4 Cleaning the pom.xml file

We now use the `org.example.Main` class as the main class of the application in two places already:

- in the `exec-maven-plugin` plugin configuration
- in the `maven-assembly-plugin` plugin configuration

We can refactor the `pom.xml` file to use a variable to specify the main class of the application.
We can add the following properties to the `pom.xml` file:

```xml

<properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <main.class>org.example.Main</main.class>
    <jar.final.name>hello-maven</jar.final.name>
</properties>
```

We can now use the `main.class` and the `jar.final.name` variable in the `exec-maven-plugin` plugin configuration and in
the `maven-assembly-plugin` plugin configuration:

```xml

<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.6.0</version>
    <configuration>
        <mainClass>${main.class}</mainClass>
    </configuration>
</plugin>
```

```xml
<plugin>
    <artifactId>maven-assembly-plugin</artifactId>
    <configuration>
        <finalName>${jar.final.name}</finalName>
        <archive>
            <manifest>
                <mainClass>${main.class}</mainClass>
            </manifest>
        </archive>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
        <appendAssemblyId>false</appendAssemblyId>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Now verify that the application still works by running the `exec:java` task.
```shell
./mvnw exec:java
```
Also Verify that you can build and run your application:
```shell
./mvnw clean package
java -jar target/hello-maven-0.1.0-SNAPSHOT.jar
```

#### 2.5. Develop Multi-Language Support in the Application

We are going to use the first parameter of the application to specify the language that the application should greet the
user in.

A greeting will be defined by three components: the greeting (e.g. `Hello`), the person (e.g. `World`) and the
punctuation (e.g. `!`).
Each component will be able to specify a text and a color. The application will print the greeting in the specified
color.
We will use `*.properties` files to specify the text and color for each component. These files will be located in
the `src/main/resources` folder.
We will name the files `ro.properties`, `en.properties`, `es.properties` and `de.properties` to specify the language of
the greeting.

Create the following files in the `src/main/resources` folder:

- `de.properties`:

```properties
greeting.text=Hallo
greeting.color=black
person.text=Freunde
person.color=yellow
punctuation.text=?!
punctuation.color=red
```

- `en.properties`:

```properties
greeting.text=Hello
greeting.color=red
person.text=friends
person.color=cyan
punctuation.text=!
punctuation.color=default
```

- `es.properties`:

```properties
greeting.text=Ola
greeting.color=red
person.text=amigos
person.color=magenta
punctuation.text=!!!
punctuation.color=red
```

- `ro.properties`:

```properties
greeting.text=Salutare
greeting.color=red
person.text=prieteni
person.color=yellow
punctuation.text=!!!
punctuation.color=blue
```

Create a PropertiesUtils class in the `org.example` package. This class will be used to load the properties files and
get
the values for the components of the greeting.

```java
package org.example;

import java.util.Properties;

public class PropertiesUtils {

    public static Properties getPropertiesForLanguage(String language) {
        Properties properties = new Properties();
        try {
            properties.load(PropertiesUtils.class.getResourceAsStream("/" + language + ".properties"));
        } catch (Exception e) {
            e.printStackTrace();
        }
        return properties;
    }
}
```

Next create a file called `Greeting.java` in the `org.example` package. This class will be used to store the components
of the greeting.

```java
package org.example;

import org.fusesource.jansi.Ansi;

import java.util.Properties;

public class Greeting {

    private final Properties properties;

    public Greeting(String language) {
        properties = PropertiesUtils.getPropertiesForLanguage(language);
    }

    public String getGreeting() {
        return properties.getProperty("greeting.text");
    }

    public Ansi.Color getGreetingColor() {
        return getAnsiColor("greeting.color");
    }

    public String getPerson() {
        return properties.getProperty("person.text");
    }

    public Ansi.Color getPersonColor() {
        return getAnsiColor("person.color");
    }

    public String getPunctuation() {
        return properties.getProperty("punctuation.text");
    }

    public Ansi.Color getPunctuationColor() {
        return getAnsiColor("punctuation.color");
    }

    private Ansi.Color getAnsiColor(String PERSON_COLOR_PROPERTY) {
        try {
            return Ansi.Color.valueOf(properties.getProperty(PERSON_COLOR_PROPERTY).toUpperCase());
        } catch (IllegalArgumentException e) {
            return Ansi.Color.DEFAULT;
        }
    }
}
```

The Greeting class will contain all the fields that a language can define for a greeting. The `getAnsiColor` method will
convert the color name to an `Ansi.Color` enum value.

Lastly, we need to update the `Main` class to use the `Greeting` class.

```java
package org.example;

import org.fusesource.jansi.Ansi;
import org.fusesource.jansi.AnsiConsole;

public class Main {
    public static void main(String[] args) {
        AnsiConsole.systemInstall();

        String language;

        if (args.length == 0) {
            language = "en";
        } else {
            language = args[0].toLowerCase();
        }
        Greeting gp = new Greeting(language);

        System.out.println(Ansi.ansi()
                .fg(gp.getGreetingColor()).a(gp.getGreeting() + " ")
                .fg(gp.getPersonColor()).a(gp.getPerson() + " ")
                .fg(gp.getPunctuationColor()).a(gp.getPunctuation())
                .reset()
        );

        AnsiConsole.systemUninstall();
    }
}
```

By default, the application will use the `en` language. If a language is specified as a parameter, the application will
use that language.

Now verify that the application still works by running the `exec:java` task. Use all languages as parameters and see if the application prompts the correct greeting:
```shell
./mvnw exec:java -Dexec.args="ro"
```

Also verify that the application works by running the following commands:
```shell
./mvnw clean package
java -jar target/hello-maven-0.1.0-SNAPSHOT.jar ro
```

#### 3. Install a distribution of the application and be able to run the project from the command line

To install a distribution of the application, we will use a `shell script` for Linux and a `cmd` file for Windows.

Create a `bin` directory in the root folder. This directory will contain the `shell script` and the `cmd` file.

Create a `hello-maven` file in the `bin` directory. This file will contain the following:
```bash
#!/usr/bin/env bash
java -jar ~/.m2/repository/org/example/hello-maven/0.1.0-SNAPSHOT/hello-maven-0.1.0-SNAPSHOT.jar "$@"
```

Also make the `hello-maven` file executable:
```bash
chmod +x bin/hello-maven
```

Create a `hello-maven.cmd` file in the `bin` directory. This file will contain the following:
```cmd
@echo off
java -jar %USERPROFILE%\.m2\repository\org\example\hello-maven\0.1.0-SNAPSHOT\hello-maven-0.1.0-SNAPSHOT.jar %*
```

To install the distribution, we will use the `mvn clean install` lifecycle command. 
```shell
./mvnw clean install
```

This task will install the application in the local Maven repository.
The local Maven repository is located in the `~/.m2/repository` folder on Linux and `%USERPROFILE%\.m2\repository` on Windows
and the application will be installed in the `org/example/hello-maven/0.1.0-SNAPSHOT` folder.

Add the `bin` folder to the `PATH` environment variable. To do this temporarily, you can run:

```bash
export PATH=$PATH:$(pwd)/bin
```
This will allow us to run the application from the command line by just using it's name.

```bash
hello-maven
```