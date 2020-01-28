
Tools required for below pipeline setup

1) Install Jenkins 

2) Installing Ansible
	sudo yum install python-pip
	sudo pip install ansible

3) Installing and configuring Helm Package Manager
	wget https://storage.googleapis.com/kubernetes-helm//var/lib/jenkins/ansible/sayarapp-deploy/deploy.yml-v2.8.1-linux-amd64.tar.gz
	tar -xzvf helm-v2.8.1-linux-amd64.tar.gz
	sudo mv linux-amd64/helm /usr/local/bin
	sudo -i -u jenkins
	mkdir .kube ; touch .kube/config
	
	helm init --upgrade	

4) Installing Docker plugin on Jenkins
	

Create a Jenkins Job
	STEP 1: Create Helm chart
			sudo su - jenkins 
			mkdir -- parents ansible/tesetapp/templates
			(Create deployment.yml and service.yml in templates)
			
	Step 2: Run Ansible_play to deploy Helm Chart 
	
	Step 3: Use Jenkinsfile as pipeline script in JOB
			
			
			
			
			
			
Jenkinsfile Explained			
			
	Step 1 — Here we define some variables:
			//default namespace on k84	
				def Namespace = "default"  
			
			// image name which will be pushed to docker registry
				def ImageName = "tesetapp/tesetapp" 
			
			// Creds of docker registry
				def Creds = "2dfd9d0d-a300-49ee-aaaf-0a3efcaa5279" 

	Step 2 — Pull/Clone updates from our version control:
			git 'https://mAyman2612@bitbucket.org/mAyman2612/ci-cd-k8s.git'
			sh "git rev-parse --short HEAD > .git/commit-id"
			imageTag= readFile('.git/commit-id').trim()
	
	Step 3 — Run Unit Tests:
			stage('RUN Unit Tests'){
									sh "npm install"
									sh "npm test"
								   }
	
	Step 4 — Docker Build and Push to docker registry
			stage('Docker Build, Push'){
										withDockerRegistry([credentialsId: "${Creds}", url: 'https://index.docker.io/v1/']) {
										sh "docker build -t ${ImageName}:${imageTag} ."
										sh "docker push ${ImageName}"
									   }
      
	Step 5 — Call the Ansible playbook to deploy on K8s
			stage('Deploy on K8s'){
									sh "ansible-playbook ./ansible/sayarapp-deploy/deploy.yml  --user=jenkins --extra-vars ImageName=${ImageName} --extra-vars imageTag=${imageTag} --extra-vars Namespace=${Namespace}"
								  }
	
Result:

	Access the application running in Kubernetes:
		kubectl get svc // to get the IP/Port of the application
	
	Now curl or telnet to check app is up and running.
		curl http://<public-node-ip>:<node-port>.			
		