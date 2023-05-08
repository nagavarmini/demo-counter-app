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

    }
}

