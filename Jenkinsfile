pipeline {
    agent any

        // {
        //   podman run -d --rm --name=DockerV1 -p 4444:22 -e "JENKINS_AGENT_SSH_PUBKEY=ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKLnmO6MeYpS0HWVi66fjh4ZDV3BHVxdjzWOWaI9539T" docker.io/jenkins/ssh-agent:alpine-jdk17  ...
        // label 'DockerV1'
       // }

    options {
        // This is required if you want to clean before build
        skipDefaultCheckout(true)
        disableConcurrentBuilds()
    }

    environment {
        // Manage Jenkins > Credentials > System > Global Credentials > ID= 'ARTIFACTORY_PUBLISH'  (user: admin ; password: ???)
        containerRegistryCredentials = credentials('ARTIFACTORY_PUBLISH')
        // set SSC's Artifactory server on default port 8082 ; instead of HC 'jack.hc-sc.gc.ca'
        // when a domain is ready for the IP ;
        containerRegistry = '3.97.240.138:8082'
        containerRegistryPull = '3.97.240.138:8082'
    }

    stages {

        stage('appmeta Info') {
            steps {
                // Clean before build inside the Jenkins container ( /var/jenkins_home/<my-pipeline>/<my-repository> )
                echo "BEFORE cleanWs()..."
                cleanWs()
                echo "AFTER cleanWs()..."

                // We need to explicitly checkout from SCM here ; ( to avoid pulling from repo while other changes occur ?)
                echo "BEFORE checkout scm"
                checkout scm
                echo "AFTER checkout scm"

                script {
                    //Get basic meta-data ; BUILD_ID is a Jenkins default var ex.: "20241011_200401" (datetime)
                    buildId = env.BUILD_ID
		    echo "BEFOR currentVersion...."
                    currentVersion="b" + (buildId ? buildId : "MANUAL-BUILD")
                    version81="8.1b" + (buildId ? buildId : "MANUAL-BUILD")
                    version82="8.2b" + (buildId ? buildId : "MANUAL-BUILD")
                    version83="8.3b" + (buildId ? buildId : "MANUAL-BUILD")
		    
		    echo "AFTER BUILD_ID versions...."

                    //Setup Artifactory connection
                    artifactoryServer = Artifactory.server 'default'
                    echo "AFTER Artifactory.server 'default'..... "
		    artifactoryGradle = Artifactory.newGradleBuild()
                    artifactoryDocker = Artifactory.docker server: artifactoryServer
                    buildInfo = Artifactory.newBuildInfo()
		    echo "AFTER Artifactory.newBuildInfo()...."
		    echo "Build Info: ${buildInfo}"
                }
            }
        }



        stage('Image') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId:'ARTIFACTORY_PUBLISH', usernameVariable: 'USR', passwordVariable: 'PWD')
                ]) {
                    sh """
                        podman login -u ${USR} -p ${PWD} ${
                            containerRegistry
                        }
                        podman pull php:7.1-apache
                        podman build -t php-base:7.1${currentVersion} -t php-base:7.1 -f dockerfile71 .
                        podman tag php-base:7.1${currentVersion} ${containerRegistry}/php/php-base:7.1${currentVersion}
                        podman tag php-base:7.1 ${containerRegistry}/php/php-base:7.1

                        podman pull php:8.1-apache
                        podman build -t php-base:8.1${currentVersion} -t php-base:8.1 -f dockerfile81 .
                        podman tag php-base:8.1${currentVersion} ${containerRegistry}/php/php-base:8.1${currentVersion}
                        podman tag php-base:8.1 ${containerRegistry}/php/php-base:8.1

                        podman pull php:8.2-apache
                        podman build -t php-base:8.2${currentVersion} -t php-base:8.2 -f dockerfile82 .
                        podman tag php-base:8.2${currentVersion} ${containerRegistry}/php/php-base:8.2${currentVersion}
                        podman tag php-base:8.2 ${containerRegistry}/php/php-base:8.2

                        podman build -t php-base:8.2${currentVersion}-mongodb -t php-base:8.2-mongodb -f dockerfile82 --target mongodb .
                        podman tag php-base:8.2${currentVersion}-mongodb ${containerRegistry}/php/php-base:8.2${currentVersion}-mongodb
                        podman tag php-base:8.2-mongodb ${containerRegistry}/php/php-base:8.2-mongodb

                        podman pull php:8.3-apache
                        podman build -t php-base:8.3${currentVersion} -t php-base:8.3 -t php-base:latest -f dockerfile83 --target base .
                        podman tag php-base:8.3${currentVersion} ${containerRegistry}/php/php-base:8.3${currentVersion}
                        podman tag php-base:8.3 ${containerRegistry}/php/php-base:8.3
                        podman tag php-base:latest ${containerRegistry}/php/php-base:latest

                        podman build -t php-base:8.3${currentVersion}-mongodb -t php-base:8.3-mongodb -t php-base:latest-mongodb -f dockerfile83 --target mongodb .
                        podman tag php-base:8.3${currentVersion}-mongodb ${containerRegistry}/php/php-base:8.3${currentVersion}-mongodb
                        podman tag php-base:8.3-mongodb ${containerRegistry}/php/php-base:8.3-mongodb
                        podman tag php-base:latest-mongodb ${containerRegistry}/php/php-base:latest-mongodb
                    """
                }
                script {
                
			echo "Hello"
		}
            }
        }




    }


}
