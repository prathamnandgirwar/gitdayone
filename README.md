docker install 

        sudo apt-get update
        sudo apt-get install docker.io -y
        sudo systemctl start docker
        sudo usermod -aG docker ubuntu
        newgrp docker
        sudo chmod 777 /var/run/docker.sock


jenkins installation


        sudo apt update
        sudo apt install fontconfig openjdk-17-jre
        java -version
        openjdk version "17.0.8" 2023-07-18
        OpenJDK Runtime Environment (build 17.0.8+7-Debian-1deb12u1)
        OpenJDK 64-Bit Server VM (build 17.0.8+7-Debian-1deb12u1, mixed mode, sharing)

#jenkins

        sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
        https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
        echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
        https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
        /etc/apt/sources.list.d/jenkins.list > /dev/null
        sudo apt-get update
        sudo apt-get install jenkins
        sudo systemctl start jenkins
        sudo systemctl enable jenkins

2. **Install Necessary Plugins in Jenkins:**

Goto Manage Jenkins →Plugins → Available Plugins →

Install below plugins

1 Eclipse Temurin Installer (Install without restart)

2 NodeJs Plugin (Install Without restart)

Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save








        pipeline{
            agent any
            tools{
                jdk 'jdk17'
                nodejs 'nodejs16'
            }
            environment {
                SCANNER_HOME=tool 'sonar-scanner'
            }
            stages {
                stage('clean workspace'){
                    steps{
                        cleanWs()
                    }
                }
                stage('Checkout from Git'){
                    steps{
                        git branch: 'main', url: 'https://github.com/prathamnandgirwar/Hostar-clown.git'
                    }
                }
               
                stage('Install Dependencies') {
                    steps {
                        sh "npm install"
                    }
                }
                 
            
                stage("Docker Build & Push"){
                    steps{
                        withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'p', usernameVariable: 'u')]) {
            // some block
                            sh "docker login -u ${env.u} -p ${env.p}"
                               sh "docker build -t hotstar ."
                               sh "docker tag hotstar prathamnandgiwar/hotstar:latest "
                               sh "docker push prathamnandgiwar/hotstar:latest "
                            
                        }
                         
                        }
                    
                }
               
                stage('Deploy to container'){
                    steps{
                        sh 'docker run -d --name hotstar -p 3000:3000 prathamnandgiwar/hotstar:latest'
                    }
                }
               
                
               
            }
        }
        
