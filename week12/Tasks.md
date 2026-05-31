# Lab Activities: Module 12 (Jenkins Fundamentals)

This laboratory module focuses on running a local, Docker-based Jenkins Controller instance, extracting the administrator password, installing recommended plugins, and executing your first Declarative Pipeline using a version-controlled `Jenkinsfile`.

---

## Challenge 1: Launch Jenkins via Docker & complete setup wizard
Run Jenkins inside a isolated container environment, persist configurations, and execute the initial unlocking sequence.

### Steps:
1. Fire up a terminal and launch the container with volume mapping:
   ```bash
   docker run -d \
     --name lpu-jenkins-controller \
     -p 8080:8080 \
     -p 50000:50000 \
     -v jenkins_data:/var/jenkins_home \
     jenkins/jenkins:lts-jdk17
   ```
2. Monitor startup container logs to retrieve the dynamically generated **Initial Setup Admin Password**:
   ```bash
   docker logs lpu-jenkins-controller
   ```
   *Look for a long hexadecimal string between lines of asterisks, for example:* `4a5f8c6e2b1d7a3f4e5c6a7b8c9d0e1f`.
3. Open your browser and navigate to `http://localhost:8080`.
4. Paste the admin password and click **Continue**.
5. Select **Install suggested plugins** and wait for the dependency installer to finish (this installs core plugins like Git, Pipeline, and Gradle support).
6. Create your Administrator User profile and finish the configuration wizard.

---

## Challenge 2: Write a Declarative `Jenkinsfile`
Construct a declarative pipeline that simulates checking out, compiling, and testing an application.

### Steps:
1. Create a directory named `week12` in your repository.
2. In the root of your project directory, create a file literally named `Jenkinsfile` (no extension).
3. Populate it with this structured, declarative configuration:
   ```groovy
   pipeline {
       agent any
       
       options {
           timeout(time: 1, unit: 'HOURS')
           buildDiscarder(logRotator(numToKeepStr: '10'))
           disableConcurrentBuilds()
       }
       
       stages {
           stage('Workspace Prep') {
               steps {
                   echo "Cleaning workspace environment..."
                   deleteDir()
               }
           }
           
           stage('Retrieve Source') {
               steps {
                   echo "Simulating Repository Checkout..."
                   echo "Branch: ${env.BRANCH_NAME ?: 'main'}"
               }
           }
           
           stage('Simulated Compile') {
               steps {
                   echo "Compiling Java source files..."
                   sh "echo 'Executing compilation commands...'"
               }
           }
           
           stage('Automated Testing') {
               steps {
                   echo "Running JUnit test assertions..."
                   sh "echo 'Tests run: 15, Failures: 0, Errors: 0'"
               }
           }
       }
       
       post {
           always {
               echo "Tearing down job resources..."
           }
           success {
               echo "CI Validation Completed Successfully!"
           }
           failure {
               echo "CI pipeline crashed. Check logs for diagnostics."
           }
       }
   }
   ```
4. Commit your changes locally:
   ```bash
   git add week12/ Jenkinsfile
   git commit -m "Add Jenkins declarative pipeline configuration"
   ```

---

## Challenge 3: Run pipeline within Jenkins dashboard
Create a pipeline job in Jenkins and link it to execute your local pipeline script.

### Steps:
1. Log in to your Jenkins Dashboard (`http://localhost:8080`).
2. Click **New Item** -> Name it `lpu-declarative-pipeline` -> Select **Pipeline** -> Click **OK**.
3. Scroll down to the **Pipeline** configuration section.
4. Set **Definition** to `Pipeline script`.
5. Copy the contents of the `Jenkinsfile` you created in Challenge 2 and paste it directly into the script editor box. *(Note: For advanced git setups, you would select 'Pipeline script from SCM' and supply your git credentials and repository URL).*
6. Click **Save**.
7. Click **Build Now** on the left menu.
8. Under **Build History**, click Build `#1`.
9. Click **Console Output** to verify the clean running log and execution stages of your Declarative pipeline!
