@Library('custom-sharedlib') _
import com.iti.MVNClass
import com.iti.DockerClass

def mvn = new MVNClass()
def docker = new DockerClass()

pipeline {
    agent {
        label 'worker-agent'
    }
    tools {
        jdk 'java-21'
        maven 'maven-3.9.16'
    }
    environment {
        docker_cred = "docker-cred"
        image_name = 'kareemelgiushy/iti-java-devops'
    }
    parameters {
        string (defaultValue: "${env.BUILD_NUMBER.toInteger() + 1}", name: 'imageTag')
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
                script {
                    mvn.buildProject("package install -DskipTests")
                }
            }
        }

        stage("test app") {
            steps {
                script {
                    mvn.runTests()
                }
            }
        }

        stage("Archive") {
            steps{
                archiveArtifacts artifacts: '**/*.jar', followSymlinks: false
            }
        }

        stage("Build Image") {
            steps {
                script {
                    docker.buildImage(image_name, params.imageTag)
                }
            }
        }

        stage("Docker login") {
            steps {
                script {
                    docker.dockerLogin(docker_cred)
                }
            }
        }

        stage("Push Image") {
            steps {
                script {
                    docker.pushImage(image_name, params.imageTag)
                }
            }
        }

    }

}