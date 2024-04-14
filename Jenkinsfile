pipeline {
    agent any
    environment {
        MVN_HOME = tool 'Jenkins_Maven_3_9_6'  // Jenkins에서 설정한 Maven 설치의 이름입니다.
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('SonarQube Scan') {
            steps {
                
                // Maven 프로젝트 스캔
                dir('sonar-scanner-maven/maven-basic') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh '${MVN_HOME}/bin/mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=MavenModule1Key \
                        -Dsonar.projectName="MavenModule 1" \
                        -Dsonar.plugins.downloadOnlyRequired=true'
                    }
                }
                
                dir('sonar-scanner-maven/maven-multilingual') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh '${MVN_HOME}/bin/mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=MavenModule2Key \
                        -Dsonar.projectName="MavenModule 2" \
                        -Dsonar.plugins.downloadOnlyRequired=true'
                    }
                }

                dir('sonar-scanner-maven/maven-multimodule') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh '${MVN_HOME}/bin/mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=MavenModule3Key \
                        -Dsonar.projectName="MavenModule 3" \
                        -Dsonar.plugins.downloadOnlyRequired=true'
                    }
                }
                
                // Gradle 프로젝트 스캔
                dir('sonar-scanner-gradle/gradle-basic') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew clean build jacocoTestReport \
                        -Dsonar.projectKey=GradleModule1Key \
                        -Dsonar.projectName="GradleModule 1" \
                        -Dsonar.plugins.downloadOnlyRequired=true sonar'
                    }
                }

                
                dir('sonar-scanner-gradle/gradle-kotlin-dsl') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew clean build jacocoTestReport \
                        -Dsonar.projectKey=GradleModule2Key \
                        -Dsonar.projectName="GradleModule 2" \
                        -Dsonar.plugins.downloadOnlyRequired=true sonar'
                    }
                }
                


                dir('sonar-scanner-gradle/gradle-multimodule') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew clean build \
                        -Dsonar.projectKey=GradleModule3Key \
                        -Dsonar.projectName="GradleModule 3" \
                        -Dsonar.plugins.downloadOnlyRequired=true sonar'
                    }
                }

                dir('sonar-scanner-gradle/gradle-multimodule-coverage') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh './gradlew clean build jacocoTestReport \
                        -Dsonar.projectKey=GradleModule4Key \
                        -Dsonar.projectName="GradleModule 4" \
                        -Dsonar.plugins.downloadOnlyRequired=true sonar'
                    }
                }

*/
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
