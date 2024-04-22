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
                script {
                    def buildStatus = currentBuild.result
                    def buildLog = sh(script: 'cat /path/to/build.log', returnStdout: true).trim()
                    def sonarQualityGate = buildLog.contains("ANALYSIS SUCCESSFUL") ? 'SUCCESS' : 'FAILED'
                    //def sonarQualityGate = currentBuild.rawBuild.getLog(100).find { it =~ /ANALYSIS SUCCESSFUL/ } != null ? 'SUCCESS' : 'FAILED'
                    def reportContent = """
                    ## 빌드 결과: ${buildStatus}
                    ## SonarQube 품질 게이트: ${sonarQualityGate}
                    ---
                    ### 빌드 로그:
                    ${currentBuild.rawBuild.getLog(100)}
                    ---
                    ### SonarQube 분석 결과:
                    [SonarQube 링크](${env.SONAR_HOST_URL}/dashboard?id=${env.SONAR_PROJECT_KEY})
                    """

                    // Redmine API를 사용하여 이슈 생성
                    def issue = new URL("${env.REDMINE_URL}/issues.json?key=${env.REDMINE_API_KEY}").openConnection().with {
                        doOutput = true
                        requestMethod = 'POST'
                        setRequestProperty("Content-Type", "application/json")
                        outputStream.withWriter { writer ->
                            writer << """
                            {
                                "issue": {
                                    "project_id": ${env.REDMINE_PROJECT_ID},
                                    "subject": "[Jenkins Pipeline] 빌드 및 SonarQube 분석 보고",
                                    "description": "${reportContent.replaceAll('"', '\\"')}"
                                }
                            }
                            """
                        }
                        content = content.text
                    }

                    if (issue.status != 201) {
                        error "Redmine 이슈 생성 실패: ${issue.content}"
                    }
                }
            }
        }
        /*
        stage('Report to Redmine') {
            steps {
                script {
                    def modules = ['MavenModule1Key', 'MavenModule2Key', 'MavenModule3Key', 'GradleModule1Key', 'GradleModule2Key', 'GradleModule3Key', 'GradleModule4Key']
                    modules.each { moduleKey ->
                        echo "Checking module: ${moduleKey}"
                        def response = sh(script: "curl -u ${env.SONARQUBE_API_KEY}: 'http://192.168.35.209:9001/api/qualitygates/project_status?projectKey=${moduleKey}'", returnStdout: true).trim()
                        echo "Response: ${response}"

                        def jsonResponse = parseJson(response)
                        
                        def issueTitle = jsonResponse.status == 'ERROR' ? "Code Quality Gate Failed for ${moduleKey}" : "Code Quality Gate Passed for ${moduleKey}"
                        def issueDescription = "The SonarQube quality gate result for module ${moduleKey} was: ${jsonResponse.status}. Check the SonarQube dashboard for more details."

                        sh """
                        curl -X POST http://192.168.35.209:3000/issues.json \\
                            -H 'Content-Type: application/json' \\
                            -H 'X-Redmine-API-Key: ${env.REDMINE_API_KEY}' \\
                            -d '{"issue": {"project_id": "testproject", "subject": "${issueTitle}", "description": "${issueDescription}"}}'
                        """
                    }
                }
            }
        }
        */
    }
}
