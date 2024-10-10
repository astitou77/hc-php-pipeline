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
                    currentVersion="b" + (buildId ? buildId : "MANUAL-BUILD")
                    version81="8.1b" + (buildId ? buildId : "MANUAL-BUILD")
                    version82="8.2b" + (buildId ? buildId : "MANUAL-BUILD")
                    version83="8.3b" + (buildId ? buildId : "MANUAL-BUILD")

                    //Setup Artifactory connection
                    artifactoryServer = Artifactory.server 'default'
                    artifactoryGradle = Artifactory.newGradleBuild()
                    artifactoryDocker = Artifactory.docker server: artifactoryServer
                    buildInfo = Artifactory.newBuildInfo()
                }
            }
        }





}
