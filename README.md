# Some hands-on experience with Jenkins

## 1. Installation and expoloring Jenkins

### 1.1 Simple job  that lists all environment variables from the host machine
![Example screenshot](./screens/1.jpg)
### 1.2 Simple job using Shell that outputs system uptime, OS version, Kernel version etc
![Example screenshot](./screens/2.jpg)
### 1.3 Simple job that triggers after previous job runs successfully. Prints build number variable
![Example screenshot](./screens/3.jpg)
![Example screenshot](./screens/4.jpg)

## 2. Plugins, folders, users, bulding, testing, pushing

### 2.1 Installed the following plugins:
 -
    - [Folders](https://plugins.jenkins.io/cloudbees-folder)
    - [Workspace cleanup](https://plugins.jenkins.io/ws-cleanup)
    - [Build timeout](https://plugins.jenkins.io/build-timeout)
    - [Git](https://plugins.jenkins.io/git/)
    - [Maven integration](https://plugins.jenkins.io/maven-plugin/)
    - [Artifactory](https://plugins.jenkins.io/artifactory/)
    - [Credentials Binding](https://plugins.jenkins.io/credentials-binding/)

### 2.2 Via Jenkins cli command
![Example screenshot](./screens/5.jpg)
### 2.3 Manually via jenkins plugin manager
![Example screenshot](./screens/6.jpg)
![Example screenshot](./screens/7.jpg)
![Example screenshot](./screens/8.jpg)

### 2.4 Jenkins job(Job A) that clones PetClinic and runing unit tests.
![Example screenshot](./screens/9.jpg)
![Example screenshot](./screens/10.jpg)
### 2.5 Jenkins job(Job B) that clones PetClinic and packages it and pushes to AWS S3
![Example screenshot](./screens/11.jpg)
![Example screenshot](./screens/12.jpg)
![Example screenshot](./screens/13.jpg)
![Example screenshot](./screens/14.jpg)

### 2.6 Created  folder "training_2" and  moved job B inside it, created user "builder" and allow them to Read, Build, Cancel jobs inside the folder
![Example screenshot](./screens/15.jpg)
![Example screenshot](./screens/16.jpg)
![Example screenshot](./screens/17.jpg)

## 3. Parameterization
### 3.1 Cloned Petclinic to main branch, created three branches: qa, stage, dev
![Example screenshot](./screens/18.jpg)
### 3.2 Created parameterized Job E that builds Petclinic project from branches: qa, stage, dev that should be used as parameters.
![Example screenshot](./screens/19.jpg)
![Example screenshot](./screens/20.jpg)
![Example screenshot](./screens/22.jpg)
![Example screenshot](./screens/23.jpg)

### 3.3 Created Jenkins agent using SSH Build Agents plugin
![Example screenshot](./screens/24.jpg)
![Example screenshot](./screens/25.jpg)

### 3.4 Created a dynamic agent using AWS Lambda Cloud Plugin and AWS lambda jenkins agent (not working)
### Builded and deoloyed AWS lambda jenkins agent 
![Example screenshot](./screens/26.jpg) 
![Example screenshot](./screens/27.jpg)
![Example screenshot](./screens/28.jpg)
### Cofigured AWS AWS Lambda Cloud 
![Example screenshot](./screens/29.jpg)
### AWS lambda Agent not lunch 
![Example screenshot](./screens/30.jpg)
![Example screenshot](./screens/error1.jpg)
![Example screenshot](./screens/error2.jpg)

## 4. Pipelines
###  Creating declarative pipeline as Job F that contains the steps from Job A and Job B
```
pipeline {
    agent {
    label 'jenslave'
        }
    tools {
          maven "maven"
    }
    stages {
        stage('Build') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                 doGenerateSubmoduleConfigurations: false, 
                 extensions: [], 
                 submoduleCfg: [], 
                 userRemoteConfigs: [[url: 'https://github.com/lubomiro/petclinic.git']]])
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
             }
        }
        stage('Test') {
            steps {
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts 'target/*.jar'
                }
            }
        stage('Deploy') {
            steps {
                s3Upload consoleLogLevel: 'INFO', 
                        dontSetBuildResultOnFailure: false, 
                        dontWaitForConcurrentBuildCompletion: false, 
                        entries: [[bucket: 'mybuilds/${JOB_NAME}-${BUILD_NUMBER}', 
                        excludedFile: '', 
                        flatten: false, 
                        gzipFiles: false, 
                        keepForever: false, 
                        managedArtifacts: false, 
                        noUploadOnFailure: true, 
                        selectedRegion: 'eu-central-1', 
                        showDirectlyInBrowser: false, 
                        sourceFile: 'target/*.jar', 
                        storageClass: 'STANDARD', 
                        uploadFromSlave: true, 
                        useServerSideEncryption: false]], 
                        pluginFailureResultConstraint: 'FAILURE', 
                        profileName: 'petclinic-bucket', 
                        userMetadata: []
            
            }
        }
    }
}
```
### Poll SCM every 3 minutes
![Example screenshot](./screens/31.jpg)
### SCM pooling with a webhook
![Example screenshot](./screens/32.jpg)
![Example screenshot](./screens/33.jpg)
![Example screenshot](./screens/34.jpg)
![Example screenshot](./screens/35.jpg)