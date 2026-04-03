pipeline {
    agent any


     tools { 
        maven 'maven3' 
    }
	
	stages {
	
    stage('Checkout') { 
            steps { 
                echo 'Cloning GIT HUB Repo' 
                // Clone the specified branch from the GitHub repository 
                git branch: 'main', url: 'https://github.com/Harinath234/mindcircuit16d-code.git'
            }   
        }
	
	
      stage('SonarQube Scan') {
      steps {
             echo 'Scanning project'
            sh 'ls -ltr'
        
        sh '''
            mvn sonar:sonar \
                -Dsonar.host.url=http://3.239.99.46:9000 \
                -Dsonar.login=squ_3d6a4c781ff2d14c64d1a8c6db5fe19c433d7a3c
        '''
         }
       }
	
        stage('Build Artifact') { 
            steps { 
                echo 'Build Artifact' 
                sh 'mvn clean package' 
            } 
        } 
	
	
	
      stage('Build Docker Image') { 
            steps { 
                echo 'Build Docker Image' 
                // Build the Docker image using the Dockerfile in the project 
                // Tag the image with the current build number 
                sh 'docker build -t harinath93811/dockerized-app:${BUILD_NUMBER} -f Dockerfile .' 
            }
        } 
	
	stage('Scan Docker Image using Trivy') { 
            steps { 
                echo 'scanning Image'    
                sh 'trivy image harinath93811/dockerized-app:${BUILD_NUMBER}' 
            } 
        } 
	
	
	  stage('Push to Docker Hub') { 
            steps { 
                script { 
                  	withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                         sh 'docker login -u Harinath234 -p ${dockerhub}' 
                      }
                    // Push the Docker image to Docker Hub 
                    sh 'docker push harinath93811/dockerized-app:${BUILD_NUMBER}' 
                    echo 'Pushed to Docker Hub' 
                } 
            } 
        } 
	
	

	stage('Update Deployment File') { 
            environment {
                GIT_REPO_NAME = "mindcircuit16d-code" 
                GIT_USER_NAME = "Harinath234" 
            } 
            steps { 
                echo 'Update Deployment File' 
                  }     
                withCredentials([string(credentialsId: 'githubtoken', variable: 'githubtoken')]) { 
                    sh ''' 
                        # Configure git user 
                        git config user.email "harinath123@gmail.com" 
                        git config user.name "Harinath" 
						
                        # Replace the tag in the deployment YAML file with the current buil  number 						
                        sed -i "s/dockerized-app:.*/dockerized-app:${BUILD_NUMBER}/g" deploymentfiles/deployment.yaml 
						
                        #Stage all changes
                        git add . 
                        # Commit changes with a message containing the build number 
                        git commit -m "Update deployment image to version ${BUILD_NUMBER}" 
                        #Push changes to the main branch of the GitHub repository 
                        git push https://${githubtoken}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main ''' 
                } 
            } 
        } 
         
    } 
}
