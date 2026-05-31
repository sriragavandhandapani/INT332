# Lab Activities: Module 13 (Advanced Jenkins)

This laboratory module focuses on configuring advanced Jenkins behaviors, mounting host Docker engines inside the Jenkins execution runtime, declaring Maven builds dynamically, and creating full containerization pipelines.

---

## Challenge 1: Maven & Docker compilation container agent
Configure a pipeline that launches a clean Maven container, downloads dependencies, and compiles your project binaries.

### Steps:
1. In your repository, create a directory named `week13`.
2. Construct a `Jenkinsfile` template that utilizes a Maven Docker Agent to perform compilation:
   ```groovy
   pipeline {
       agent {
           docker {
               image 'maven:3.9.5-eclipse-temurin-17'
               // Maps local home cache to speed up successive builds
               args '-v /tmp/m2-cache:/root/.m2'
           }
       }
       
       stages {
           stage('Print Version') {
               steps {
                   sh 'mvn -version'
                   sh 'java -version'
               }
           }
           
           stage('Maven Compile') {
               steps {
                   echo "Running Maven compilation inside isolated container..."
                   // In a real project, checkout would occur first
                   // sh 'mvn clean compile'
               }
           }
       }
   }
   ```

---

## Challenge 2: Containerizing applications inside Jenkins Pipeline
Create a pipeline that logs in to Docker Hub using encrypted credentials, builds a Docker image, and pushes it.

### Steps:
1. Open Jenkins Dashboard -> Click **Manage Jenkins** -> **Credentials** -> **System** -> **Global credentials (unrestricted)**.
2. Click **Add Credentials**:
   - **Kind:** Username with password
   - **Scope:** Global
   - **ID:** `docker-hub-credentials`
   - **Username:** *Your Docker Hub username*
   - **Password:** *Your Docker Hub Personal Access Token (PAT)*
3. Click **Create**.
4. In your project repository, construct a `Jenkinsfile` utilizing this credential key to compile and push an image:
   ```groovy
   pipeline {
       agent any
       
       environment {
           REGISTRY_URL = 'docker.io'
           IMAGE_NAME   = 'sriragavandhandapani/int332-demo'
           IMAGE_TAG    = "build-${env.BUILD_NUMBER}"
       }
       
       stages {
           stage('Checkout') {
               steps {
                   echo "Fetching latest source code changes..."
               }
           }
           
           stage('Build Docker Image') {
               steps {
                   echo "Running docker build command..."
                   sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                   sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
               }
           }
           
           stage('Publish Container') {
               steps {
                   // Injecting credentials securely
                   withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', 
                                                     passwordVariable: 'DOCKER_PASSWORD', 
                                                     usernameVariable: 'DOCKER_USERNAME')]) {
                       
                       echo "Authenticating to Docker Hub..."
                       sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin"
                       
                       echo "Pushing built images to repository..."
                       sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                       sh "docker push ${IMAGE_NAME}:latest"
                   }
               }
           }
       }
       
       post {
           always {
               echo "Tearing down system credentials..."
               sh "docker logout"
           }
       }
   }
   ```

---

## Challenge 3: Setting up automatic triggering (`pollSCM`)
Configure automatic build execution checks within Jenkins to scan for code updates.

### Steps:
1. Open your pipeline configuration page inside Jenkins.
2. Under **Build Triggers**, check the box labeled **Poll SCM**.
3. In the **Schedule** text field, add a cron configuration instructing Jenkins to poll your Git repo for changes every 2 minutes:
   ```text
   */2 * * * *
   ```
4. Click **Save**.
5. Make a minor modification to your project files locally, commit, and push.
6. Observe the Jenkins dashboard. Within 2 minutes, Jenkins will notice the commit history has drifted, and launch a build automatically!
