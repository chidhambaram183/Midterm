# Midterm

html dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80

pipeline 
pipeline {
 agent any
 environment {
 DOCKER_IMAGE = "<<dockerhubusername>>/html-demo"
 }
 stages {
 stage('Clone Code') {
 steps {
 git branch: 'main',
 url: '<<UserGITHUB repository>>’
 }
 }
 stage('Build Docker Image') {
 steps {
 bat 'docker build -t %DOCKER_IMAGE% .'
 }
 }
 stage('Push Image') {
 steps {
 withCredentials([usernamePassword(credentialsId: 'dockerhub', 
usernameVariable: 'USER', passwordVariable: 'PASS')]) {
 bat 'docker login -u %USER% -p %PASS%'
 bat 'docker push %DOCKER_IMAGE%'
 }
 }
 }
 stage('Deploy to Kubernetes') {
 steps {
 withCredentials([file(credentialsId: 'kuberconfig', variable: 
'KUBECONFIG')]) {
 bat '''
 set KUBECONFIG=%KUBECONFIG%
 kubectl apply -f deployment.yaml --validate=false
 '''
 }
 }
 }
 }
}


java
FROM eclipse-temurin:17-jdk
WORKDIR /app
COPY target/simple-java-app-1.0.jar app.jar
CMD ["java", "-jar", "app.jar"]

Pipleline Script:
pipeline {
 agent any
 environment {
 DOCKER_IMAGE = " yourdockerhubusername/java-app:latest"
 }
 stages {
 stage('Clone Repository') {
 steps {
 git branch: 'main',
 url: 'https://github.com/your-reponame/simple-java-app.git'
 }
 }
 stage('Build with Maven') {
 steps {
 bat 'mvn clean package'
 }
 }
 stage('Build Docker Image') {
 steps {
 bat "docker build -t %DOCKER_IMAGE% ."
 }
 }
 stage('Push to DockerHub') {
 steps {
 withCredentials([usernamePassword(
 credentialsId: 'dockerhub-creds',
 usernameVariable: 'DOCKER_USER',
 passwordVariable: 'DOCKER_PASS'
 )]) {
 bat """
 docker login -u %DOCKER_USER% -p %DOCKER_PASS%
 docker push %DOCKER_IMAGE%
 """
 }
 }
 }
 stage('Deploy to Kubernetes') {
 steps {
 bat 'kubectl apply -f deployment.yaml'
 }
 }
 }
 post {
 success {
 echo 'CI/CD Pipeline executed successfully!'
 }
 failure {
 echo 'Pipeline failed. Please check logs.'
 }
 }
}


Jenkins and maven 

pipeline {
 agent any
 tools {
 maven 'M3'
 }
 stages {
 stage('Checkout Git') {
 steps {
 git branch: 'main',
 url: '<your-repository-url>'
 }
 }
 stage('Build and Test') {
 steps {
 bat 'mvn clean test'
 }
 }
 }
}
