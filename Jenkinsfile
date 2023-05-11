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
                                repository: 'demo app', 
                                version: '1.0.0'
                }
            }
        }
    

        



    }
}

