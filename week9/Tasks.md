# Lab Activities: Module 9 (GitHub Actions Fundamentals)

This laboratory module focuses on scaffolding your first GitHub Actions directory, configuring event-driven triggers, setting up multi-job pipelines, and implementing manual variables.

---

## Challenge 1: Scaffolding the Actions directory & basic workflow
Construct the standard GitHub Action directory structure and initialize a workflow that runs simple checks on push.

### Steps:
1. Navigate to the root of your git repository.
2. Construct the standard `.github/workflows` folder path:
   ```bash
   mkdir -p .github/workflows
   ```
3. Inside `.github/workflows/`, create a file named `hello-world-ci.yml`.
4. Add the following minimal configuration:
   ```yaml
   name: Hello World Validation CI
   
   on: [push, pull_request]
   
   jobs:
     verify-environment:
       runs-on: ubuntu-latest
       steps:
         - name: Welcome Message
           run: echo "Initiating CI System Verification Pipeline..."
           
         - name: Environment Diagnostics
           run: |
             echo "System OS: $RUNNER_OS"
             echo "Trigger Event: $GITHUB_EVENT_NAME"
             echo "Triggered By: $GITHUB_ACTOR"
             echo "Target Branch: $GITHUB_REF"
   ```
5. Stage, commit, and push this file to GitHub:
   ```bash
   git add .github/workflows/hello-world-ci.yml
   git commit -m "Add Hello World CI workflow"
   ```
6. Navigate to your repository page on GitHub, click the **Actions** tab, and view your pipeline running!

---

## Challenge 2: Sequential and dependent multi-job pipelines
By default, jobs in a workflow execute in parallel. Design a workflow where a `test` job only runs if the `compile` job succeeds.

### Steps:
1. In `.github/workflows/`, create a file named `multi-job-pipeline.yml`.
2. Add the following content, illustrating job dependency using the `needs` key:
   ```yaml
   name: Dependent Multi-Job Pipeline
   
   on:
     push:
       branches:
         - main
   
   jobs:
     compile:
       runs-on: ubuntu-latest
       steps:
         - name: Scaffolding Artifact
           run: |
             echo "Compiling application binaries..."
             mkdir -p build/
             echo "Class Bytecode: V1" > build/App.class
   
     test:
       runs-on: ubuntu-latest
       needs: compile # Demands compile job to succeed first
       steps:
         - name: Run Test Suites
           run: |
             echo "Running test suite against compiled binaries..."
             echo "Tests Passed: 100%"
   
     deploy:
       runs-on: ubuntu-latest
       needs: test # Demands test job to succeed first
       steps:
         - name: Deploy Binary
           run: echo "Deploying package to staging environments..."
   ```

---

## Challenge 3: Manual triggers with parameters (`workflow_dispatch`)
Build a manual workflow that prompts developers for input variables before launching, which is useful for triggering manual test runs or production deployments.

### Steps:
1. In `.github/workflows/`, create a file named `manual-trigger.yml`.
2. Add the following structure:
   ```yaml
   name: Manual Deployment Pipeline
   
   on:
     workflow_dispatch:
       inputs:
         target_environment:
           description: 'Deploy to environment'
           required: true
           default: 'staging'
           type: choice
           options:
             - staging
             - UAT
             - production
         debug_level:
           description: 'Log Verbosity'
           required: false
           default: 'info'
           type: string
   
   jobs:
     deploy-app:
       runs-on: ubuntu-latest
       steps:
         - name: Execute Manual Run
           run: |
             echo "Deploying application manually..."
             echo "Target Environment Chosen: ${{ github.event.inputs.target_environment }}"
             echo "Log Verbosity Level: ${{ github.event.inputs.debug_level }}"
   ```
3. Push to GitHub.
4. Go to **Actions** -> **Manual Deployment Pipeline** -> Click the **Run workflow** dropdown on the right side of the screen.
5. Select the environment and log verbosity, and run! Verify the inputs in the runner logs.
