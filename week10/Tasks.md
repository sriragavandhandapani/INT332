# Lab Activities: Module 10 (Advanced GitHub Actions)

This laboratory module focuses on scaling test suites across a matrix of operating systems and runtimes, configuring build caches to accelerate execution, and sharing artifacts/variables across independent workflow jobs.

---

## Challenge 1: Matrix testing across platforms and runtimes
Write a pipeline that automatically compiles and runs unit tests on multiple operating systems (Ubuntu Linux, Windows, and macOS) against different Java JDK releases (17 and 21).

### Steps:
1. Create a directory named `week10/` inside your repository.
2. In your `.github/workflows/` directory, create a file named `matrix-ci.yml` (Note: for manual practice, this acts as the template).
3. Insert the following code:
   ```yaml
   name: Cross-Platform Matrix CI
   
   on:
     push:
       branches: [ main ]
   
   jobs:
     matrix-build:
       name: Run on ${{ matrix.os }} with JDK ${{ matrix.java-version }}
       runs-on: ${{ matrix.os }}
       
       strategy:
         fail-fast: false
         matrix:
           os: [ubuntu-latest, windows-latest, macos-latest]
           java-version: [17, 21]
   
       steps:
         - name: Checkout Code
           uses: actions/checkout@v4
   
         - name: Initialize JDK ${{ matrix.java-version }}
           uses: actions/setup-java@v3
           with:
             java-version: ${{ matrix.java-version }}
             distribution: 'temurin'
             cache: 'maven'
   
         - name: Diagnostic Verification
           run: java -version
   ```

---

## Challenge 2: Maven dependency caching verification
Observe and measure the performance gains by enabling the native caching feature in your workflow execution.

### Steps:
1. Configure a Java/Maven repository setup (similar to Week 7 or 8).
2. Write a standard build step without caching:
   ```yaml
   - name: Scaffolding JDK
     uses: actions/setup-java@v3
     with:
       java-version: '17'
       distribution: 'temurin'
       # No cache defined
   ```
3. Commit and run. Note the build execution time in the Actions console (specifically the download step where Maven fetches JUnit, Guava, and other libraries).
4. Update the step to enable caching:
   ```yaml
   - name: Scaffolding JDK with Caching
     uses: actions/setup-java@v3
     with:
       java-version: '17'
       distribution: 'temurin'
       cache: 'maven' # Enforce automatic ~/.m2 caching
   ```
5. Trigger the pipeline twice. 
   - **Run 1:** Cache miss (stores new files to cache).
   - **Run 2:** Cache hit (restores files from cache).
6. Compare the execution times. You should observe a significant drop in execution duration (often 2x to 5x faster).

---

## Challenge 3: Multi-job pipeline passing artifacts & variables
Build a pipeline where:
1. **Job 1 (Compile & Package):** Builds the executable fat JAR, extracts a git commit short SHA, and uploads the JAR.
2. **Job 2 (Release / Security Check):** Downloads the JAR, runs a security diagnostic, and outputs validation.

### Steps:
Create a workflow YAML template:
```yaml
name: Multi-Job Packaging and Diagnostics

on:
  push:
    branches: [ main ]

jobs:
  package:
    runs-on: ubuntu-latest
    outputs:
      artifact_tag: ${{ steps.sha-generator.outputs.short_sha }}
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Generate Release ID
        id: sha-generator
        run: |
          SHA_VALUE=$(echo "${{ github.sha }}" | cut -c1-7)
          echo "short_sha=$SHA_VALUE" >> "$GITHUB_OUTPUT"

      - name: Scaffold Mock Package
        run: |
          mkdir -p target/
          echo "Compiled Application Code version: ${{ github.sha }}" > target/app-release.jar

      - name: Upload Build Artifact
        uses: actions/upload-artifact@v3
        with:
          name: packaged-binary
          path: target/app-release.jar

  security-scan:
    runs-on: ubuntu-latest
    needs: package
    steps:
      - name: Download Build Artifact
        uses: actions/download-artifact@v3
        with:
          name: packaged-binary
          path: bin/

      - name: Perform Diagnostics
        run: |
          echo "Reviewing file contents of artifact..."
          cat bin/app-release.jar
          echo "Package ID: ${{ needs.package.outputs.artifact_tag }}"
          echo "Scan successfully verified!"
```
