pipeline{
    agent none
    stages{
        
        stage('Deploy'){
            agent {
                label 'master'
            }
            options {
                skipDefaultCheckout()
            }
            steps {
                sh 'docker stop sggastos-backend'
                sh 'docker rm sggastos-backend'
                sh 'docker run -dit --name sggastos-backend -p 8017:4000 node'
                sh 'docker exec sggastos-backend git clone https://github.com/rickiwasho/sggastos'
                sh 'docker exec sggastos-backend bash -c cd sggastos/backend/'
                sh 'docker exec sggastos-backend bash -c pwd'
                sh 'docker exec sggastos-backend bash ls'

                sh 'echo ________________________________________'

                sh 'rm -rf /var/www/sggastos'
                sh 'mkdir /var/www/sggastos'
                sh 'cp -Rp frontend/build/** /var/www/sggastos/'
                sh 'echo hola > /var/www/sggastos/test.html'
                sh 'docker stop sggastos || true && docker rm sggastos || true'
                sh 'docker run -dit --name sggastos -p 8016:80 -v /var/www/sggastos/:/usr/local/apache2/htdocs/ httpd:2.4'



                
            }
        }
    }
}

