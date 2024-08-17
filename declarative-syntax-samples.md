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


## Triggers

### triggers sample

```groovy
pipeline {
    agent any
    triggers {
        cron('H/15 * * * *')
    }
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

## Tools

### Loading maven using tools

```groovy
pipeline {
    agent any
    tools {
        maven 'Maven 3.6.3'
        jdk 'jdk11'
    }
    stages {
         stage('Load Tools') {
              steps {
                 sh "mvn --version"
                 sh "java --version" 
              }
         }
     }
}
```


## When

### When block with parameters

```groovy
pipeline {
     agent any
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['qa', 'uat', 'prod'], description: 'Pick the environment')
    }
     stages {
         stage('build') {
             when {
                 expression {
                     return params.ENVIRONMENT == 'uat'
                 }
             }
              steps {
                 echo "Working on dev branch"
              }
         }
     }
}
```

### when block with environment variables

```groovy
pipeline {
    agent any
    environment {
        ENV = 'uat'
    }
    stages {
        stage('Welcome Step') {
            when {
                environment name: 'ENV', value: 'uat'
             }
            steps {
                echo "Testing in environment"
            }
        }
    }
}
```

## Code Checkout 

```groovy
pipeline {
    agent any
    stages {
         stage('Code Checkout') {
              steps {
                  checkout scmGit(
                     branches: [[name: "master"]],
                     userRemoteConfigs: [[credentialsId: 'ssh-keys',
                         url: 'git@github.com:aditya2001/selenium-java-cucumber.git']])
              }
         }
     }
}
```

## Post with multiple conditional blocks

```groovy
pipeline {
     agent any
     stages {
         stage('build step') {
              steps {
                 echo "Build stage is running"
              }
         }
     }
     post {
         always {
             always {
                 archiveArtifacts artifacts: "test output/PdfReport/ExtentPdf.pdf", onlyIfSuccessful: false
                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'test output/PdfReport', reportFiles: 'ExtentPdf.pdf', reportName: 'PDF Report', reportTitles: ''])
             }
         }
         success {
              echo "Job is successful"
         }
         unstable {
              echo "unstable build. Try fix it"
         }
         failure {
             echo "build failed"
         }
     }
}
```

## Triggers

### triggers sample

```groovy
pipeline {
    agent any
    triggers {
        cron('H/15 * * * *')
    }
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```
