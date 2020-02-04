pipeline {
    agent {
        label 'master'
    }
    stages {
        stage('Prepare') {
            agent {
                docker { image 'node:alpine' }
            }
            steps {
                dir('code/backend'){
                    sh 'npm install'
                }
                dir('code/frontend'){
                    sh 'npm install'
                }
                echo 'Prepare'
            }
        }
        stage('Build') {
            agent {
                docker { image 'node:alpine' }
            }
            steps {
                dir('code/backend'){
                    sh 'npm run build'
                }
                dir('code/frontend'){
                    sh 'npm run build'
                }
                echo 'Build'      
            }
        }
        stage('Static Analysis') {
            agent {
                docker { image 'node:alpine' }
            }
            steps {
                dir('code/backend'){
                    sh 'npm run lint'
                }
                dir('code/frontend'){
                    sh 'npm run lint'
                }
                echo 'Analyze' 
            }
        }
        stage('Unit Test') {
            agent {
                docker { image 'node:alpine' }
            }
            steps {
                dir('code/backend'){
                    sh 'npm run test'
                }
                echo 'Test'
            }
        }
        stage('e2e Test') {
            steps {    
                dir('ci/code'){
                    sh 'docker-compose -f docker-compose-e2e.yml build'
                    sh 'docker-compose -f docker-compose-e2e.yml up -d frontend backend'
                    script {
                        sh 'docker-compose -f docker-compose-e2e.yml up e2e'
                        status_code = sh ( script: "docker inspect code_e2e_1 --format='{{.State.ExitCode}}'", returnStdout: true).trim();
                        if (status_code == '1'){
                            error('e2e test failed.')
                        }
                    }
                }         
                echo 'e2e Test'
            }
            post {
                always {
                    dir('ci/code'){
                        sh 'docker-compose -f docker-compose-e2e.yml down --rmi=all -v'
                        echo 'Cleanup'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {                
                echo 'Deploy'
            }
        }
    }
}
