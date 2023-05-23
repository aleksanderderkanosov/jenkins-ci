UNITY_PATH = '%unity%' // create system environment var pointing to unity install path

pipeline {
    //Definition of env variables that can be used throughout the pipeline job
    environment {
        // Github data
        String gitUrl = "https://github.com/aleksanderderkanosov/jenkins-ci.git"
        String gitCredentials = "jenkins-private-key" // set a PrivateKey credential if your repo is private
        
        // Unity tool installation
        UNITY_EXECUTABLE = '%unity%' // create system environment var pointing to unity install path
        
        // Unity data
        UNITY_ID_EMAIL = credentials('jenkins-id-for-unity-email')
        UNITY_ID_PASSWORD = credentials('jenkins-id-for-unity-password')
        UNITY_ID_LICENSE = credentials('jenkins-id-for-unity-license')

        // Unity Build params
        BUILD_NAME = "Android-${currentBuild.number}"
        String buildTarget = "Android"
        String outputFolder = "CurrentBuild"

        //PARAMETERS DATA
        IS_DEVELOPMENT_BUILD = "${params.developmentBuild}"

        // Add other EnvVars here
    }
    //Options: add timestamp to job logs and limiting the number of builds to be kept.
    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: "10"))
    }
    //Variable inputs that modify the behavior of the job
    parameters {
        string(name: 'gitBranch', defaultValue: 'development', description: 'Set the branch.')
        booleanParam(name: 'developmentBuild', defaultValue: true, description: 'Choose the buildType.')
    }
    //Tag Selector of the agent that will run the build job
    agent any
    /*agent {
        node {
            // Jenkins node to be used must have the label android
            label "android"
        }
    }*/
    //The steps necessary to generate the desired build
    stages {
        stage('Git Pull') {
            steps {
                echo "Git pull repo"
                script {
                    try {
                        git url: "${gitUrl}", branch: "${gitBranch}", credentialsId: "${gitCredentials}"
                    } catch (e) {
                        currentBuild.result = "FAILED"
                        echo "JOB FAILED: The selected branch does not exists."
                    }
                }
            }
        }
        stage('Build Application') {
            steps {
                script {
                    echo "create Application output folder..."
                    bat 'mkdir %outputFolder%'

                    echo "Buld App..."
                    bat '%UNITY_EXECUTABLE% -projectPath %CD% -quit -batchmode -nographics -buildTarget %buildTarget% -customBuildPath %CD%\\%outputFolder%\\ -customBuildName %BUILD_NAME% -executeMethod BuildCommand.PerformBuilds'
                }
            }
        }
    }
    //Any action we want to perform after all the steps have succeeded or failed
    post {
        success {
            echo "Success :)"
        }
        failure {
            echo "Failure!"
        }
    }
}

