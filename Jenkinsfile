pipeline {
    agent any
    environment {
        MVN_HOME = tool 'Jenkins_Maven_3_9_6'  // Jenkins에서 설정한 Maven 설치의 이름입니다.
        REDMINE_URL = 'http://192.168.11.18:3000'
        REDMINE_API_KEY = credentials('redmine-api-key')
        REDMINE_PROJECT_ID = 'testproject'

        SONAR_HOST_URL = "http://192.168.11.18:9001" // SonarQube 서버 URL
        SONARQUBE_API_KEY = credentials('sonarqube_token')
    }

    def getSonarIssues(projectKey) {
        def response = httpRequest url: "${env.SONAR_HOST_URL}/api/issues/search?projectKeys=${projectKey}&severities=BLOCKER,CRITICAL,MAJOR&ps=5",
                                auth: 'BASIC', user: env.SONARQUBE_API_KEY, 
                                contentType: 'APPLICATION_JSON'
        return parseJson(response.content).issues
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
                
                /*
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
                        sh '${MVN_HOME}/bin/mvn -T clean verify sonar:sonar \
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
        stage('Redmine 보고') {
            steps {
                def issues = getSonarIssues('MavenModule1Key') // SonarQube 프로젝트 키를 입력
                def issueList = """<ul>"""
                issues.each { issue ->
                    issueList += """<li>[${issue.severity}] ${issue.component}: ${issue.message} (Line ${issue.line})</li>"""
                }
                issueList += """</ul>"""

                redmineIssueTracker(
                    projectKey: 'testproject',
                    type: 'Task',
                    subject: "[Jenkins Pipeline] 빌드 및 SonarQube 분석 보고",
                    description: """
                        ## 빌드 결과: ${currentBuild.result}
                        ## SonarQube 품질 게이트: ${/* SonarQube 게이트 상태 확인 로직 */}
                        ---
                        ### 빌드 로그 (일부):
                        ${currentBuild.rawBuild.getLog(100)}
                        ---
                        ### SonarQube 분석 결과:
                        [SonarQube 링크](${env.SONAR_HOST_URL}/dashboard?id=${env.SONAR_PROJECT_KEY})
                        ---
                        ### 주요 이슈:
                        ${issueList}
                    """
                )
            }
        }
    }
}
