pipeline {
    agent any
    environment {
		GIT_CREDENTIALS = credentials ('git-credentials')
        DOCKER_CREDENTIALS = 'docker-credentials'
        image = "juancastaneda20/movie-analyst-api" + ":$BUILD_NUMBER"
        testImage = ''
        dockerImage = ''
        dockerContainer=''
	}
    stages {
        stage('Clone git repo') {
            steps {
                echo 'Cloning thegit repository'
                git credentialsId: 'git-credentials', url: 'git@gitlab.com:movie-analyst20/movie-analyst-api.git'
            }
        }
        stage('build'){
            parallel{
                stage('build develop and qa'){
                    when { anyOf { branch 'develop'; branch 'qa' } }
                    steps {
                        script{
                            testImage = docker.build image+"-test"
                        }
                    } 
                }
                stage('build master and staging'){
                    when { anyOf { branch 'master'; branch 'staging' } }
                    steps {
                        script{
                            dockerImage = docker.build image
                        }
                    }  
                }
            }
        }
        stage('test'){
            when { anyOf { branch 'develop'; branch 'qa' } }
            steps {
                script{
                    sh 'docker run --name test_container --entrypoint /bin/sh ' + image + '-test -c "ls"'
                    sh 'docker rm test_container'
                    sh 'docker rmi' + image + '-test'
                } 
            }
        }
        stage('deploy'){
            when {branch 'master'}
            steps{
                script{
                    def hosts = ['10.1.3.4']
                    hosts.each(){
                        sshagent(credentials : ['ssh-key']) {
                            sh 'docker save ' + image + ' | ssh -C juancas20@' + it + ' docker load'
                            sh 'ssh -v -o StrictHostKeyChecking=no -T juancas20@' + it + ' docker run -p 3000:3000 --name backend ' + image
                        }
                    }
                }
            }
        }
    }
}