@Library('jenkins_option@main') _

pipeline {
    agent any
    environment {
        MVN_HOME = tool 'Jenkins_Maven_3_9_6'
        folderName = "gradle-basic"
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
                dir("sonar-scanner-gradle/${folderName}") {
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
                    echo "Calling saveData with folderName: ${folderName}"

                    def directory = new File("/var/jenkins_home/shared_data")
                    if (!directory.exists()) {
                        directory.mkdirs()
                    }
                    new File(directory, "sonarProject.txt").write(folderName)

                    dir("sonar-scanner-gradle/${folderName}") {
                        withSonarQubeEnv('SonarQube Server') {
                            sh "./gradlew \
                                -Dsonar.projectKey=${folderName} \
                                -Dsonar.projectName='${folderName}' \
                                -Dsonar.plugins.downloadOnlyRequired=true sonar"
                        }
                    }

                    /*
                    dir("sonar-scanner-maven/${folderName}") {
                        withSonarQubeEnv('SonarQube Server') {
                            sh "${MVN_HOME}/bin/mvn sonar:sonar \
                            -Dsonar.projectKey=${folderName} \
                            -Dsonar.projectName='${folderName}' \
                            -Dsonar.plugins.downloadOnlyRequired=true"
                        }
                    }
                    */
                }
            }
        }

        stage('Trigger Redmine Pipeline') {
            steps {
                script {
                    build job: 'report_redmine'
                    // 예시 build job: 'B-파이프라인-이름', parameters: [string(name: 'Param1', value: 'Value1')]

                    archiveArtifacts artifacts: 'path/to/artifacts/**', onlyIfSuccessful: true
                }
            }
        }
    }
}