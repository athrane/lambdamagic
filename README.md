# Lambda Magic

## Mod Requirements

### Java Requirements

This mod requires **Java 21** to run. Mojang ships Java 21 to end users starting in Minecraft version 1.20.5, so mods should target this version to ensure compatibility.

#### How to Check Your Java Version
To verify that you have Java 21 installed, run the following command in your terminal:
```bash
java -version
```

If the output does not indicate Java 21, you will need to install or update your Java version.

## NeoForge Installation information

### Installation information
This template repository can be directly cloned to get you started with a new
mod. Simply create a new repository cloned from this one, by following the
instructions provided by [GitHub](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template).

Once you have your clone, simply open the repository in the IDE of your choice. The usual recommendation for an IDE is either IntelliJ IDEA or Eclipse.

If at any point you are missing libraries in your IDE, or you've run into problems you can
run `gradlew --refresh-dependencies` to refresh the local cache. `gradlew clean` to reset everything 
{this does not affect your code} and then start the process again.

### Mapping Names
By default, the MDK is configured to use the official mapping names from Mojang for methods and fields 
in the Minecraft codebase. These names are covered by a specific license. All modders should be aware of this
license. For the latest license text, refer to the mapping file itself, or the reference copy here:
https://github.com/NeoForged/NeoForm/blob/main/Mojang.md

### Additional Resources: 
* Community Documentation: https://docs.neoforged.net/  
* NeoForged Discord: https://discord.neoforged.net/
* Getting Started with NeoForge: https://docs.neoforged.net/docs/gettingstarted/

## Gradle Project Setup

### Verifying the Gradle installation
The `./gradlew` command runs the Gradle Wrapper script included in your project. Here's what it does:

1. **Ensures Gradle is Installed**:
    - The Gradle Wrapper (`gradlew`) is a script that downloads and uses a specific version of Gradle defined in your project (in `gradle/wrapper/gradle-wrapper.properties`).
    - This ensures that everyone working on the project uses the same Gradle version, regardless of whether Gradle is installed on their system.

2. **Executes Gradle Tasks**:
    - When you run `./gradlew`, it executes Gradle tasks specified in your project. For example:
        - `./gradlew build`: Builds the project.
        - `./gradlew clean`: Cleans the build directory.
        - `./gradlew test`: Runs the tests.

3. **Cross-Platform Compatibility**:
    - The wrapper script (`gradlew`) works on all platforms (Linux, macOS, Windows). On Windows, you would use `gradlew.bat`.

In summary, `./gradlew` ensures a consistent Gradle environment and allows you to run Gradle tasks without requiring a separate Gradle installation.

Run the following command to get Gradle to run:
   ```bash
   ./gradlew
   ```

### Refresh Dependencies
1. **Run the following command to download all required dependencies**:
   ```bash
   ./gradlew --refresh-dependencies
   ```

2. **Clean the Project (Optional)**:
To ensure a clean build environment, you can run:
   ```bash
   ./gradlew clean
   ```

3. **Build the Project**:
Verify that everything is set up correctly by building the project:
   ```bash
   ./gradlew build
   ```

These steps will ensure that your development environment is fully initialized and ready to use in the Codespace.

## Gradle Configuration
Gradle is used to manage the build process for this project. The configuration is split between two files: `gradle.properties` and `build.gradle`. Here's how they work together:

### gradle.properties
The `gradle.properties` file is used to define project-wide properties that can be referenced in the `build.gradle` file. These properties make it easier to manage and update configuration values without modifying the build script directly. Common properties include:

- **`mod_version`**: Specifies the version of the mod.
- **`mod_group_id`**: Defines the group ID for the mod, typically used for publishing.
- **`mod_id`**: The unique identifier for the mod.
- **`mod_name`**: The display name of the mod.
- **`mod_license`**: The license under which the mod is distributed.
- **`loader_version_range`**: Specifies the compatible version range for the mod loader.
- **`mod_authors`**: A list of authors for the mod.
- **`mod_description`**: A brief description of the mod.

Example `gradle.properties` file:
```properties
mod_version=1.0.0
mod_group_id=com.example.lambdamagic
mod_id=lambdamagic
mod_name=Lambda Magic
mod_license=MIT
loader_version_range=[1.0,2.0)
mod_authors=Author1, Author2
mod_description=A magical mod for Minecraft.
```

### build.gradle
The `build.gradle` file is the main build script for the project. It uses the properties defined in `gradle.properties` to configure various aspects of the build process. This ensures that the build script remains clean and easy to maintain.

#### How `build.gradle` Uses `gradle.properties`
1. **Version and Group Configuration**:
   The `mod_version` and `mod_group_id` properties are used to set the version and group ID for the project:
   ```gradle
   version = mod_version
   group = mod_group_id
   ```

2. **Resource Processing**:
   The `build.gradle` file uses the properties to replace placeholders in resource files, such as `META-INF/neoforge.mods.toml`. This ensures that the resource files are dynamically updated with the correct values during the build process:
   ```gradle
   tasks.withType(ProcessResources).configureEach {
       var replaceProperties = [
           loader_version_range   : loader_version_range,
           mod_id                 : mod_id,
           mod_name               : mod_name,
           mod_license            : mod_license,
           mod_version            : mod_version,
           mod_authors            : mod_authors,
           mod_description        : mod_description
       ]
       inputs.properties replaceProperties

       filesMatching(['META-INF/neoforge.mods.toml']) {
           expand replaceProperties
       }
   }
   ```

3. **Java Toolchain Configuration**:
   The `build.gradle` file specifies the Java version required for the project:
   ```gradle
   java.toolchain.languageVersion = JavaLanguageVersion.of(21)
   ```

4. **Publishing Configuration**:
   The `mod_version` and `mod_group_id` properties are also used in the `publishing` block to configure the Maven publication:
   ```gradle
   publishing {
       publications {
           register('mavenJava', MavenPublication) {
               groupId = mod_group_id
               artifactId = mod_id
               version = mod_version
           }
       }
   }
   ```

### settings.gradle

This file configures the Gradle build settings for the project. It contains two main sections:

#### Plugin Management
```groovy
pluginManagement {
    repositories {
        mavenLocal()
        gradlePluginPortal()
        maven { url = 'https://maven.neoforged.net/releases' }
    }
}
```
This section configures where Gradle looks for plugins:
- `mavenLocal()`: Checks the local Maven repository first
- `gradlePluginPortal()`: Uses the central Gradle Plugin Portal
- A custom Maven repository for NeoForged releases

#### Plugins
```groovy
plugins {
    id 'org.gradle.toolchains.foojay-resolver-convention' version '0.9.0'
}
```
This section applies the Foojay Resolver Convention plugin, which helps manage Java toolchains in the project. 
This plugin simplifies the process of downloading and using specific Java versions.

### Summary
The `gradle.properties` file centralizes configuration values, while the `build.gradle` file uses these values to define the build process. This separation of concerns makes the project easier to maintain and update. If you need to change a property (e.g., the mod version), you can do so in `gradle.properties` without modifying the `build.gradle` file.


