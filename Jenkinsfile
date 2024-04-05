pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Scan') {
            steps {
                // Maven 프로젝트 스캔
                /*
                dir('mavenProject1') {
                    withSonarQubeEnv('YourSonarQubeServerName') {
                        sh 'mvn sonar:sonar'
                    }
                }
                dir('mavenProject2') {
                    withSonarQubeEnv('YourSonarQubeServerName') {
                        sh 'mvn sonar:sonar'
                    }
                }
                */
                
                // Gradle 프로젝트 스캔
                dir('sonar-scanner-gradle/gradle-basic') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew sonarqube -Dsonar.projectKey=module1Key -Dsonar.projectName="Module 1"'
                    }
                }

                /*
                // Docker 프로젝트 소스 코드 스캔
                dir('dockerProject') {
                    withSonarQubeEnv('YourSonarQubeServerName') {
                        sh 'sonar-scanner'
                    }
                }
                */
            }
        }
    }
}