import groovy.json.JsonOutput
import groovy.json.JsonSlurper

pipeline {
    agent any
    environment {
        MVN_HOME = tool 'Jenkins_Maven_3_9_6'  // Jenkins에서 설정한 Maven 설치의 이름입니다.
        REDMINE_API_KEY = credentials('redmine-api-key')
        REDMINE_URL = 'http://192.168.35.209:3000'
        SONARQUBE_API_KEY = credentials('sonarqube-api-key')
        SONARQUBE_HOST = 'http://192.168.35.209:9001'
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
                        sh '${MVN_HOME}/bin/mvn -T 1C clean verify sonar:sonar \
                        -Dsonar.projectKey=MavenModule1Key \
                        -Dsonar.projectName="MavenModule 1" \
                        -Dsonar.plugins.downloadOnlyRequired=true'
                    }
                }
                /*
                dir('sonar-scanner-maven/maven-multilingual') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh '${MVN_HOME}/bin/mvn -T 1C clean verify sonar:sonar \
                        -Dsonar.projectKey=MavenModule2Key \
                        -Dsonar.projectName="MavenModule 2" \
                        -Dsonar.plugins.downloadOnlyRequired=true'
                    }
                }

                dir('sonar-scanner-maven/maven-multimodule') {
                    withSonarQubeEnv('SonarQube Server') {
                        sh '${MVN_HOME}/bin/mvn -T 1C clean verify sonar:sonar \
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

        stage('Report to Redmine') {
            steps {
                script {
                    // SonarQube API 호출을 curl로 변경
                    def sonarQubeResults = sh(script: """
                        curl -X GET -H "Authorization: Token ${env.SONARQUBE_API_KEY}" \\
                            "${env.SONARQUBE_HOST}/api/measures/component?component=MavenModule1Key&metricKeys=ncloc,complexity,violations"
                    """, returnStdout: true).trim()
        
                    def jsonSlurper = new JsonSlurper()
                    def sonarResultData = jsonSlurper.parseText(sonarQubeResults)
                    def metrics = sonarResultData.component.measures.collectEntries { [(it.metric): it.value] }
        
                    echo "SonarQube Results: NCLOC: ${metrics.ncloc}, Complexity: ${metrics.complexity}, Violations: ${metrics.violations}"
        
                    // Redmine 이슈 생성 요청을 curl로 변경
                    def jsonRequestBody = JsonOutput.toJson([
                        issue: [
                            project_id: "testproject",
                            subject: "Build and Analysis Report",
                            description: "Build status: ${buildStatus}\nSonarQube analysis:\n- Lines of Code: ${metrics.ncloc}\n- Complexity: ${metrics.complexity}\n- Violations: ${metrics.violations}\nView more details at ${env.SONARQUBE_HOST}/dashboard?id=MavenModule1Key",
                            status_id: buildStatus == 'SUCCESS' ? '3' : '5'
                        ]
                    ])
        
                    sh """
                        curl -X POST -H "Content-Type: application/json" \\
                             -H "X-Redmine-API-Key: ${env.REDMINE_API_KEY}" \\
                             -d '${jsonRequestBody}' \\
                             "${env.REDMINE_URL}/issues.json"
                    """
                }
            }
        }
    }
}
