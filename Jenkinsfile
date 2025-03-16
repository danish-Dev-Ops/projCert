pipeline {
    agent any
    stages {
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
                    git clone https://github.com/your-repo/projCert.git
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
