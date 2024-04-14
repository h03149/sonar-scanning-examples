import groovy.json.JsonOutput
import groovy.json.JsonSlurper

pipeline {
    agent any
    environment {
        MVN_HOME = tool 'Jenkins_Maven_3_9_6'  // Jenkins에서 설정한 Maven 설치의 이름입니다.
        REDMINE_API_KEY = credentials('redmine-api-key')
        REDMINE_URL = 'http://192.168.35.209:3000'
        SONARQUBE_API_KEY = credentials('sonarqube_token')
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
                    def buildStatus = currentBuild.result ?: 'SUCCESS'

                    // SonarQube 결과 가져오기
                    def sonarQubeResults = httpRequest(
                        url: "${env.SONARQUBE_HOST}/api/measures/component?component=${env.SONAR_PROJECT_KEY}&metricKeys=ncloc,complexity,violations",
                        authentication: env.SONARQUBE_API_KEY,
                        acceptType: 'APPLICATION_JSON'
                    ).content
                    def jsonSlurper = new JsonSlurper()
                    def sonarResultData = jsonSlurper.parseText(sonarQubeResults)
                    def metrics = sonarResultData.component.measures.collectEntries { [(it.metric): it.value] }

                    def description = """
                    Build status: ${buildStatus}
                    SonarQube analysis:
                    - Lines of Code: ${metrics.ncloc}
                    - Complexity: ${metrics.complexity}
                    - Violations: ${metrics.violations}
                    View more details at ${env.SONARQUBE_HOST}/dashboard?id=${env.SONAR_PROJECT_KEY}
                    """

                    // Redmine 이슈 생성 요청
                    httpRequest(
                        url: "${REDMINE_URL}/issues.json",
                        httpMode: 'POST',
                        contentType: 'APPLICATION_JSON',
                        acceptType: 'APPLICATION_JSON',
                        requestBody: JsonOutput.toJson([
                            issue: [
                                project_id: "testproject",
                                subject: "Build and Analysis Report",
                                description: description,
                                status_id: buildStatus == 'SUCCESS' ? '3' : '5'
                            ]
                        ]),
                        customHeaders: [[name: 'X-Redmine-API-Key', value: REDMINE_API_KEY]]
                    )
                }
            }
        }
    }
}
