pipeline{
    agent none
            stages {
                stage ('Unit Tests'){
                    agent {
                        label 'apache'
                    }
                    steps{
                        sh 'ant -f test.xml -v'
                        junit 'reports/result.xml'
                    }
                }
                stage('build'){
                    agent {
                        label 'apache'
                    }
                    steps{
                        sh 'ant -f build.xml -v'
                    }
                    post {
                        always{
                            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
                        }
                    }

                }
                stage('deploy'){
                    agent {
                        label 'apache'
                    }
                    steps{
                        sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
                        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
                    }
                }
                stage("Running on CentOS"){
                    agent{
                        label 'CentOS'
                    }
                    steps {
                        sh "wget http://irodriguez1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
                        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
                    }
                }
                stage("Test on debian"){
                    agent {
                        docker 'openjdk:8u121-jre'
                    }
                    steps{
                        sh "wget http://irodriguez1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
                        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
                    }
                }
                stage('Promote to Green'){
                    agent {
                        label 'apache'
                    }
                    when{
                        branch 'master'
                    }
                    steps{
                        sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
                    }
                }
                stage('promote Development Branch to Master'){
                    agent{
                        label 'apache'
                    }
                    when{
                        breanch 'development'
                    }
                    steps{
                        echo "stashing Any Local Changes"
                        sh 'git stash'
                        echo 'checking out development branch'
                        sh 'git checkout development'
                        echo 'checking out master branch'
                        sh 'git checkout master'
                        echo 'merging development into master branch'
                        sh 'git merge development'
                        echo 'pushing to origin master'
                        sh 'git push origin master'

                    }
                }
            }
}

