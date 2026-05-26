pipeline {
    agent {
        label 'worker-agent'
    }
    tools {
        jdk 'java-21'
        maven 'maven-3.9.16'
    }
    environment {
        docker_cred = credentials("docker-cred")
    }

    stages {
        stage('Debug Permissions') {
            steps {
                sh 'whoami'
                sh 'groups'
                sh 'ls -l /var/run/docker.sock'
            }
        }

        stage("build java app") {
            steps {
                sh "mvn package install -DskipTests"
            }
        }

        stage("test app") {
            steps {
                sh "mvn test"
            }
        }

        stage("Archive") {
            steps{
                archiveArtifacts artifacts: '**/*.jar', followSymlinks: false
            }
        }

        stage("Build Image") {
            steps {
                sh "docker build -t kareemelgiushy/iti-java-devops:v1 ."
            }
        }

        stage("Docker login") {
            steps {
                sh "docker login -u ${docker_cred_USR} -p ${docker_cred_PSW}"
            }
        }

        stage("Push Image") {
            steps {
                sh "docker push kareemelgiushy/iti-java-devops:v1"
            }
        }

    }

}