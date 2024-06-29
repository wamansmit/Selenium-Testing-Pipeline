# Selenium-Testing-Pipeline

Sure, here's a step-by-step guide to set up a CI/CD pipeline using IntelliJ, GitHub, Jenkins, Node.js, Selenium for testing, and Docker for deployment.

### Prerequisites

1. **IntelliJ IDEA** installed.
2. **Node.js** installed.
3. **GitHub** account.
4. **Jenkins** installed and running.
5. **Docker** installed.

### Project Setup in IntelliJ

1. **Create a new Node.js project**:
   - Open IntelliJ IDEA.
   - Create a new project and select Node.js.
   - Initialize your project using `npm init`.

2. **Add Selenium for Testing**:
   - Install Selenium WebDriver: `npm install selenium-webdriver`.

3. **Create a Basic Test**:
   - Create a `test` folder.
   - Add a basic Selenium test script in the `test` folder.
     ```javascript
     // test/sampleTest.js
     const { Builder, By, until } = require('selenium-webdriver');

     async function example() {
         let driver = await new Builder().forBrowser('chrome').build();
         try {
             await driver.get('http://www.google.com/ncr');
             await driver.findElement(By.name('q')).sendKeys('webdriver');
             await driver.findElement(By.name('btnK')).click();
             await driver.wait(until.titleIs('webdriver - Google Search'), 1000);
         } finally {
             await driver.quit();
         }
     }

     example();
     ```

### GitHub Setup

1. **Create a new repository** on GitHub.
2. **Push your IntelliJ project** to the GitHub repository.
   ```sh
   git init
   git add .
   git commit -m "Initial commit"
   git remote add origin <your-repo-url>
   git push -u origin master
   ```

### Jenkins Setup

1. **Install Jenkins**:
   - Follow the [Jenkins installation guide](https://www.jenkins.io/doc/book/installing/).

2. **Install Required Plugins**:
   - Go to Jenkins dashboard > Manage Jenkins > Manage Plugins.
   - Install `Git Plugin`, `NodeJS Plugin`, `Docker Plugin`, and `Selenium Plugin`.

3. **Configure Node.js**:
   - Go to Jenkins dashboard > Manage Jenkins > Global Tool Configuration.
   - Add Node.js installations (name it e.g., `NodeJS`).

### Creating Jenkins Pipeline

1. **Create a New Pipeline Job**:
   - Go to Jenkins dashboard.
   - Create a new item > Pipeline.

2. **Configure Pipeline**:
   - In the Pipeline section, select `Pipeline script from SCM`.
   - Choose `Git` and provide your repository URL.
   - Add the branch to build (e.g., `*/master`).

3. **Add Jenkinsfile to Project**:
   - In your IntelliJ project, create a `Jenkinsfile` in the root directory.
     ```groovy
     pipeline {
         agent any

         tools {
             nodejs "NodeJS"
         }

         stages {
             stage('Checkout') {
                 steps {
                     git url: 'https://github.com/your-username/your-repo.git', branch: 'master'
                 }
             }
             stage('Install Dependencies') {
                 steps {
                     sh 'npm install'
                 }
             }
             stage('Run Tests') {
                 steps {
                     sh 'npm test'
                 }
             }
             stage('Build Docker Image') {
                 steps {
                     script {
                         docker.build('your-image-name:latest')
                     }
                 }
             }
             stage('Deploy') {
                 steps {
                     sh 'docker run -d -p 8080:8080 your-image-name:latest'
                 }
             }
         }
     }
     ```

4. **Push Jenkinsfile to GitHub**:
   ```sh
   git add Jenkinsfile
   git commit -m "Add Jenkinsfile"
   git push
   ```

5. **Run the Pipeline**:
   - Go to Jenkins dashboard > your pipeline job.
   - Click on `Build Now`.

### Dockerfile

1. **Create a Dockerfile in your IntelliJ project**:
   ```Dockerfile
   # Use the official Node.js image.
   FROM node:14

   # Create and change to the app directory.
   WORKDIR /usr/src/app

   # Copy application dependency manifests to the container image.
   COPY package*.json ./

   # Install dependencies.
   RUN npm install

   # Copy local code to the container image.
   COPY . .

   # Run the web service on container startup.
   CMD [ "node", "app.js" ]
   ```

2. **Push Dockerfile to GitHub**:
   ```sh
   git add Dockerfile
   git commit -m "Add Dockerfile"
   git push
   ```

This setup will allow you to automatically run tests and build Docker images for deployment whenever you push changes to your GitHub repository.
