pipeline {
    agent any

    stages {
        stage('Clone Git Repository') {
            steps {
                script {
                    def clonePath = "/home/ec2-user/build_${env.BUILD_NUMBER}"
                    dir(clonePath) {
                        git branch: 'main', url: 'https://github.com/Meenakshi0812/sshagent-remote-server.git'
                    }
                }
            }
        }

        stage('Create Zip File') {
            steps {
                script {
                    def folderName = "build_${env.BUILD_NUMBER}"
                    def zipFileName = "${folderName}.zip"
                    sh "cd /home/ec2-user && zip -r ${zipFileName} ${folderName}/*"
                }
            }
        }

        stage('Copy Zip File to Remote Server') {
            steps {
                sshagent(credentials: ['your-ssh-credentials-id']) {
                    script {
                        def zipFileName = "build_${env.BUILD_NUMBER}.zip"
                        def destinationPath = "/var/www/html/"
                        sh "scp -o StrictHostKeyChecking=no /home/ubuntu/${zipFileName} ubuntu@35.173.124.74:${destinationPath}"
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: ['aplication-server-ssh']) {
                    script {
                        def folderName = "build_${env.BUILD_NUMBER}"
                        def zipFileName = "${folderName}.zip"
                        def destinationPath = "/var/www/html/"

                        sh "ssh -o StrictHostKeyChecking=no ubuntu@35.173.124.74 'unzip -o ${destinationPath}${zipFileName} -d ${destinationPath}'"
                        sh "ssh -o StrictHostKeyChecking=no ubuntu@35.173.124.74 'ln -sfn ${destinationPath}${folderName} ${destinationPath}code'"
                    }
                }
            }
        }
    }
}
