# Jenkins Declarative Pipeline

Traditionally, Jenkins jobs were created using Jenkins UI called FreeStyle jobs. In Jenkins 2.0, Jenkins introduced a new way to create jobs using the technique called pipeline as code. 
In pipeline as code technique, jobs are created using a script file that contains the steps to be executed by the job like code checkout, mvn test, reports generation commands. In Jenkins, that scripted file is called Jenkinsfile.

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
The triggers directive defines the automated ways in which the Pipeline should be re-triggered.

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
A section defining tools to auto-install and put on the PATH. Inside pipeline block or stage block.
The tool name must be pre-configured in Jenkins under Manage Jenkins → Global Tool Configuration.
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
The when directive allows the Pipeline to determine whether the stage should be executed depending on the given condition. 
Allowed inside stage directive.

### When block with expression

The parameters directive provides a list of parameters which a user should provide when triggering the Pipeline. The values for these user-specified parameters are made available to Pipeline steps via the params object
Allowed only inside pipeline block.

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
                  echo "Hello ${params.ENVIRONMENT}"
              }
         }
     }
}
```

### when block with environment variables

The environment directive specifies a sequence of key-value pairs which will be defined as environment variables for the all steps, or stage-specific steps, depending on where the environment directive is located within the Pipeline.

```groovy
pipeline {
    agent any
    environment {
        ENV = 'uat'
    }
    stages {
        stage('Welcome Step') {
            environment {
                ENV = 'uat'
            }
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

### When block with branch

```groovy
pipeline {
     agent any
     stages {
         stage('build') {
              when {
                  branch 'dev'
              }
              steps {
                 echo "Working on dev branch"
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

The triggers directive defines the automated ways in which the Pipeline should be re-triggered. For Pipelines which are integrated with a source such as GitHub or Bitbucket, triggers may not be necessary as webhooks-based integration will likely already be present. Currently the only two available triggers are cron and pollSCM.

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
