# Jenkins Declarative Pipeline

## First Declarative Pipeline

```groovy
pipeline {
    agent any
    stages {
        stage('Welcome Step') {
            steps { 
                echo 'Welcome to UI Test'
            }
        }
    }
}
```
#### 1. All Declarative pipeline start with a pipeline section.
#### 2. Declarative pipeline is defined as series of stages where each stage runs a series of steps.


## tools

### Loading maven using tools

```groovy
pipeline {
    agent none
    tools {
        maven 'Maven 3.6.3'
        jdk 'jdk8'
    }
    stages {
         stage('Load Tools') {
              steps {
                 sh "mvn --version"
                 sd "java --version" 
              }
         }
     }
}
```