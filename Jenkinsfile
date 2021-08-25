pipeline {
    agent any
    environment {
		GIT_CREDENTIALS = credentials ('git-credentials')
        DOCKER_CREDENTIALS = 'docker-credentials'
        image = "juancastaneda20/movie-analyst-api" + ":$BUILD_NUMBER"
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
                stage('develop and qa'){
                    when { anyOf { branch 'develop'; branch 'qa' } }
                    steps {
                        echo 'Building docker the image for develop and qa'
                    } 
                }
                stage('master and staging'){
                    when { anyOf { branch 'master'; branch 'staging' } }
                    steps {
                        echo 'Building docker the image for master and staging'
                    }  
                }
            }
        }
        stage('test'){
            when { anyOf { branch 'develop'; branch 'qa' } }
            steps {
                echo 'Runing the tests for develop and qa'
            }
        }
    }
}