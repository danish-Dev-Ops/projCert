pipeline {
    agent any
    stages {

        // Stage 0: Add Test Server's Host Key
        stage('Add Host Key') {
            steps {
                sh '''
                    mkdir -p ~/.ssh
                    ssh-keyscan 172.31.17.227 >> ~/.ssh/known_hosts
                '''
            }
        }
        
        // Stage 1: Install Puppet Agent on Test Server
        stage('Install Puppet Agent') {
            steps {
                ansiblePlaybook(
                    playbook: 'ansible-playbooks/puppet_install.yml',
                    inventory: 'ansible-playbooks/inventory.txt',
                    credentialsId: 'ssh-credentials'
                )
            }
        }

        // Stage 2: Install Docker on Test Server
        stage('Install Docker') {
            steps {
                ansiblePlaybook(
                    playbook: 'ansible-playbooks/docker_install.yml',
                    inventory: 'ansible-playbooks/inventory.txt',
                    credentialsId: 'ssh-credentials'
                )
            }
        }

        // Stage 3: Build and Deploy PHP App on Test Server
        stage('Build and Deploy PHP App') {
            steps {
                sh '''
                    cd projCert
                    docker build -t php-app .
                    docker run -d -p 80:80 --name php-app-container php-app
                '''
            }
            post {
                failure {
                    sh 'docker rm -f php-app-container'
                }
            }
        }

        // Stage 4: Deploy to Production Server (Optional)
    }
}
