pipeline{
	agent none
	environment{
		DOCKER_IMAGE="spring-boot-app"
		REGISTRY="<docker-registry-url>"
		REGISTRY_CREDENTIALS="docker-credentials-id"
	}

	stages{
		stage('Build')
		  agent{label 'build-server'}
		  steps{
			script{
				sh 'docker build -t $DOCKER_IMAGE:latest'
				}
			}
		}

		stage('Test'){
			agent{label 'test-server'}
			steps{
				script{
					sh 'docker run --rm $DOCKER_IMAGE:latest mvn test'
					}
				}
			}
		stage('Push to Registry'){
		  when{
			expression {return env.REGISTRY != null}
			}
			agent{label 'build-server'}
			steps{
				script{
					docker.withRegistry("$REGISTRY","REGISTRY_CREDENTIALS"){
					sh "docker tag $DOCKER_IMAGE:latest $REGISTRY/$DOCKER_IMAGE:latest"
					sh "docker push $REGISTRY/$DOCKER_IMAGE:latest"
					}
		
					}
				}
		}
		stage('Deploy'){
			agent{lable 'deploy-server'}
			steps{
				script{
					sh "docker run -d -p 8080:8080 $REGISTRY/$DOCKER_IMAGE:latest"
					}
				}
			}


		post{
			always{cleanWs()}
			failure{echo 'pipeline failed'}
			sucess{echo 'pipeline completed sucessfully'}
			}
}