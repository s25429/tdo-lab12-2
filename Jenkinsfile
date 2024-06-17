pipeline {
    agent {
        node {
            label 'docker-agent-cpp'
        }
    }

    environment {
        SONARQUBE_SERVER = 'LocalSonarQube'
        BUILD_WRAPPER_URL = 'http://localhost:9000/static/cpp/build-wrapper-linux-x86.zip'
    }

    stages {
        stage('Download Build Wrapper') {
            steps {
                sh 'curl -o build-wrapper-linux-x86.zip $BUILD_WRAPPER_URL'
                sh 'unzip build-wrapper-linux-x86.zip -d build-wrapper'
                sh 'chmod +x build-wrapper/build-wrapper-linux-x86-64'
            }
        }
        stage('Build') {
            steps {
                sh './build-wrapper/build-wrapper-linux-x86-64 --out-dir bw-output g++ -o main.out main.cpp'
            }
        }
        // stage('Run') {
        //     steps {
        //         sh './main.out'
        //     }
        // }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(SONARQUBE_SERVER) {
                    sh 'sonar-scanner \
                        -Dsonar.projectKey=my-cpp-project \
                        -Dsonar.sources=. \
                        -Dsonar.cfamily.build-wrapper-output=bw-output \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=$SONAR_AUTH_TOKEN'
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }
}