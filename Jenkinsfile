@Library('jenkins_option@main') _

pipeline {
    agent any
    environment {
        MVN_HOME = tool 'Jenkins_Maven_3_9_6'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                // Gradle 프로젝트 스캔
                dir('sonar-scanner-gradle/gradle-basic') {
                    sh 'chmod +x ./gradlew' // gradlew 실행 권한 부여
                    sh './gradlew clean build'
                }

                /*
                // Maven 프로젝트 스캔
                dir('sonar-scanner-maven/maven-basic') {
                    sh '${MVN_HOME}/bin/mvn clean verify'
                }
                */
            }
        }

        stage('Sonarqube Scan') {
            steps {
                script {
                    def folderName = "gradle-basic" // 폴더명 지정
                    DataHandler.saveData('sonarProject', ${folderName})

                    echo "$env.GIT_URL"
                    echo "$repoName"
                    dir('sonar-scanner-gradle/${folderName}') {
                        withSonarQubeEnv('SonarQube Server') {
                            sh './gradlew \
                                -Dsonar.projectKey=${folderName} \
                                -Dsonar.projectName="${folderName}" \
                                -Dsonar.plugins.downloadOnlyRequired=true sonar' 
                        }
                    }

                    /*
                    dir('sonar-scanner-maven/${folderName}') {
                        withSonarQubeEnv('SonarQube Server') {
                            sh '${MVN_HOME}/bin/mvn sonar:sonar \
                            -Dsonar.projectKey=${folderName} \
                            -Dsonar.projectName="${folderName}" \
                            -Dsonar.plugins.downloadOnlyRequired=true'
                        }
                    }
                    */
                }
            }
        }

/*
        stage('Trigger Redmine Pipeline') {
            steps {
                script {
                    def upstreamJobs = ["SonarQube Scan"]

                    def allUpstreamSuccess = upstreamJobs.every { jobName ->
                        def job = Jenkins.instance.getItemByFullName(jobName)
                        def lastBuild = job.getLastBuild()
                        return lastBuild && lastBuild.result == 'SUCCESS'
                    }

                    if (allUpstreamSuccess) {
                        build job: 'report_redmine', wait: false
                        echo "report_redmine 파이프라인 트리거 완료"
                    } else {
                        echo "SonarQube Scan 단계가 실패하여 report_redmine 파이프라인을 트리거하지 않습니다."
                    }
                }
            }
        }
        */
    }
}