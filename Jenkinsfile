pipeline{

agent any 

stages{

    stage('Git Checkout'){

        steps{

            git branch: 'main', changelog: false, poll: false, url: 'https://github.com/nagavarmini/demo-counter-app.git'
        }
    }
    stage('UNIT testing'){

        steps{

            sh '/Users/varminimunagala/Downloads/apache-maven-3.9.1/bin/mvn test'
        }
    }
    stage('Integration testing'){

        steps{

            sh '/Users/varminimunagala/Downloads/apache-maven-3.9.1/bin/mvn verify -DskipUnitTests'
        }
    }
    stage('Maven Build'){

        steps{

            sh '/Users/varminimunagala/Downloads/apache-maven-3.9.1/bin/mvn clean install'
        }
    }
    stage('Static code analysis'){

        steps{
            script{
                 withSonarQubeEnv(credentialsId: 'sonar-api') {
                 sh '/Users/varminimunagala/Downloads/apache-maven-3.9.1/bin/mvn clean package sonar:sonar'
               }
            }
        }
    }
    stage('Quality gate status'){

         steps{
            script{
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api'
               }
            }
        }
        stage('upload war file to nexus'){
            steps{
                script{

                    def readPomVersion = readMavenPom file: 'pom.xml'  
                    def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snap" : "demoapp"  
                    nexusArtifactUploader artifacts:
                     [
                        [
                            artifactId: 'springboot',
                             classifier: '', file: 'target/Uber.jar',
                              type: 'jar']
                              ], 
                                credentialsId: 'nexus-auth',
                                groupId: 'com.example', 
                                nexusUrl: '3.80.117.207:8081',
                                nexusVersion: 'nexus3',
                                protocol: 'http',
                                repository: nexusRepo, 
                                version: "${readPomVersion.version}"
                }
            }
        }
    

        stage('Docker Image Build'){

            steps{

                script{
                    sh '/usr/local/bin/docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                    sh '/usr/local/bin/docker image tag $JOB_NAME:v1.$BUILD_ID varmini/$JOB_NAME:v1.$BUILD_ID'
                    sh '/usr/local/bin/docker image tag $JOB_NAME:v1.$BUILD_ID varmini/$JOB_NAME:latest'


                }
            }
        }

        stage('push image to the dockerhub'){

            steps{

                script{
                         withCredentials([string(credentialsId: 'docker_creds', variable: 'docker_hub_credentials')]) {

                            sh '/usr/local/bin/docker login -u varmini -p ${docker_hub_credentials}'
                            sh '/usr/local/bin/docker image push varmini/$JOB_NAME:v1.$BUILD_ID'
                            sh '/usr/local/bin/docker image push varmini/$JOB_NAME:latest'

                        
                    }

                }
            }
        }



    }
}

