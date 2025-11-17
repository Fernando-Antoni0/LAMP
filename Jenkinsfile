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
            agent {
                docker {
                    image 'python:3.10-slim'
                    args '--entrypoint="" -e HOME=/tmp'
                }
            }
            steps {
                echo 'Validando código Ansible...'
                sh '''
                    #Instalar herramientas
                    pip install ansible-lint yamllint ansible-core
                    
                    #Actualizar PATH para esta sesión
                    export PATH=$PATH:/tmp/.local/bin
                    
                    #Instalar colecciones
                    ansible-galaxy collection install community.docker amazon.aws community.windows
                    
                    #Ejecutar validaciones
                    ansible-lint
                    yamllint .
                '''
            }
        }

        stage('3. Provisionar y Desplegar LAMP') {
            steps {
                echo 'Ejecutando Playbook Principal (LAMP)...'
                
                withCredentials([
                    sshUserPrivateKey(credentialsId: 'aws-ec2-key-leomed', keyFileVariable: 'KEY_AWS_LEOMED'),
                    sshUserPrivateKey(credentialsId: 'aws-ec2-key-fernando', keyFileVariable: 'KEY_AWS_FERNANDO'),
                    sshUserPrivateKey(credentialsId: 'target-node-key', keyFileVariable: 'KEY_TARGET')
                ]) {
                    sh """
                        eval \$(ssh-agent -s)
                        ssh-add ${env.KEY_AWS_LEOMED}
                        ssh-add ${env.KEY_AWS_FERNANDO}
                        ssh-add ${env.KEY_TARGET}
                        
                        # Ejecutamos Ansible
                        ansible-playbook -i hosts.ini playbook.yml
                        
                        # Limpieza
                        ssh-agent -k
                    """
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