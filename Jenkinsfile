pipeline {
    agent any

    stages {
        stage('1. Checkout Code') {
            steps {
                echo 'Obteniendo código desde Git...'
                checkout scm
            }
        }

        stage('2. Lint Ansible Code') {
            steps {
                echo 'Validando código Ansible...'
                sh '''
                    export PYTHONPATH=/var/lib/jenkins/.local/lib/python3.10/site-packages
                    export ANSIBLE_COLLECTIONS_PATHS=/var/lib/jenkins/.ansible/collections
                    ansible-lint playbook.yml
                    '''
                sh 'yamllint .'
            }
        }

        
        stage('3. Provisionar y Desplegar LAMP') {
            steps {
                echo 'Ejecutando Playbook Principal (LAMP)...'
                
                sshAgent(credentials: ['aws-ec2-key-leomed', 'aws-ec2-key-fernando', 'target-node-key']) {
                    
                    sh "ansible-playbook -i hosts.ini playbook.yml"
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline de LAMP finalizado.'
        }
        success {
            echo '¡Despliegue de LAMP Exitoso!'
        }
        failure {
            echo '¡Despliegue de LAMP Fallido!'
        }
    }
}