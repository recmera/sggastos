pipeline{
    agent none
    stages{
        stage('Frontend: install & build'){
            agent {
                docker {
                    image 'node:14-alpine'
                    args '-p 3016:3000'
                }
            }
            environment {
                HOME = '.'
            }
            stages {
                stage('Install') {
                    steps {
                        dir ('frontend') {
                            sh 'npm install'
                        }
                    }
                }
                stage('Build') {
                    steps {
                        dir ('frontend') {
                            sh 'npm run build'
                        }
                    }
                }
                stage('Archive') {
                    steps {
                        dir ('frontend'){
                            archiveArtifacts 'build/**'
                        }
                    }
                }
            }
        }
        
        stage('Deploy'){
            agent {
                label 'master'
            }
            options {
                skipDefaultCheckout()
            }
            steps {
                sh 'rm -rf /var/www/sggastos'
                sh 'mkdir /var/www/sggastos'
                sh 'cp -Rp frontend/build/** /var/www/sggastos/'
                sh 'echo hola > /var/www/sggastos/test.html'
                sh 'docker stop sggastos || true && docker rm sggastos || true'
                sh 'docker run -dit --name sggastos -p 8016:80 -v /var/www/sggastos/:/usr/local/apache2/htdocs/ httpd:2.4'

                sh 'docker stop sggastos-backend'
                sh 'docker rm sggastos-backend'
                sh 'docker run -dit --name sggastos-backend -p 8017:3000 node'
                sh 'docker exec sggastos-backend git clone https://github.com/rickiwasho/sggastos'
                sh 'docker exec -w /sggastos/backend sggastos-backend cp util/dotenv_template .env'
                
                sh 'docker exec -w /sggastos/backend sggastos-backend npm install'
                sh 'docker exec -w /sggastos/backend sggastos-backend npm start'

                sh 'docker stop sggastos-db'
                sh 'docker rm sggastos-db'
                sh 'docker run --name sggastos-db -e POSTGRES_PASSWORD=mipassword -p 5464:5432 -d postgres:alpine'
                sh 'docker exec sggastos-db wget https://diegosandoval.net/random/sggastos.sql'
                sh 'sleep 10'
                sh 'docker exec sggastos-db psql -U postgres -a -f sggastos.sql'
            }
        }
    }
}

