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
                git branch: 'main', url: 'https://github.com/devopstraininghub/mindcircuit16d.git'
            }   
        } 
	
	
      stage('SonarQube Scan') {
      steps {
             echo 'Scanning project'
            sh 'ls -ltr'
        
        sh '''
            mvn sonar:sonar \
                -Dsonar.host.url=http://13.220.82.203:9000 \
                -Dsonar.login=squ_f21d96bcca2ac0be12229663009aa66157fa8e45
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
                sh 'docker build -t devopshubg333/batch16d:${BUILD_NUMBER} -f Dockerfile .' 
            } 
        } 
	
	stage('Scan Docker Image using Trivy') { 
            steps { 
                echo 'scanning Image' 
               
               
                sh 'trivy image devopshubg333/batch16d:${BUILD_NUMBER}' 
            } 
        } 
	
	
	  stage('Push to Docker Hub') { 
            steps { 
                script { 
                 
                  	withCredentials([string(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
                         sh 'docker login -u devopshubg333 -p ${dockerhub}' 
	
                      }
                    // Push the Docker image to Docker Hub 
                    sh 'docker push devopshubg333/batch16d:${BUILD_NUMBER}' 
                    echo 'Pushed to Docker Hub' 
                } 
            } 
        } 
	
	

	stage('Update Deployment File') { 
            environment { 
                GIT_REPO_NAME = "mindcircuit16d" 
                GIT_USER_NAME = "devopstraininghub" 
            } 
            steps { 
                echo 'Update Deployment File' 
				
				withCredentials([string(credentialsId: 'githubtoken', variable: 'githubtoken')]) {
    // some block
}
              
                withCredentials([string(credentialsId: 'githubtoken', variable: 'githubtoken')]) { 
                    sh ''' 
                        # Configure git user 
                        git config user.email "madhuxxxx123@gmail.com" 
                        git config user.name "Madhu" 
						
                        # Replace the tag in the deployment YAML file with the current buil  number 						
                        sed -i "s/batch16d:.*/batch16d:${BUILD_NUMBER}/g" deploymentfiles/deployment.yaml 
						
						
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
