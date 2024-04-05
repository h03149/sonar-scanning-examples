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
                        sh './gradlew clean build jacocoTestReport \
                        -Dsonar.projectKey=module1Key \
                        -Dsonar.projectName="Module 1" sonarqube '
                    }
                }
                dir('sonar-scanner-gradle/gradle-kotlin-dsl') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew clean build jacocoTestReport \
                        -Dsonar.projectKey=module2Key \
                        -Dsonar.projectName="Module 2" sonarqube '
                    }
                }

                dir('sonar-scanner-gradle/gradle-multimodule') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew clean build jacocoTestReport \
                        -Dsonar.projectKey=module3Key \
                        -Dsonar.projectName="Module 3" sonarqube '
                    }
                }

                dir('sonar-scanner-gradle/gradle-multimodule-coverage') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew clean build jacocoTestReport \
                        -Dsonar.projectKey=module4Key \
                        -Dsonar.projectName="Module 4" sonarqube '
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