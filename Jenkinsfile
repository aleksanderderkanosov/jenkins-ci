pipeline {

    //Variable inputs that modify the behavior of the job
    parameters {
        choice(name: 'buildTarget', choices: ['Android', 'StandaloneWindows'], description: "Choose the target platform.")
        string(name: 'gitBranch', defaultValue: 'master', description: 'Set the branch.')
        booleanParam(name: 'developmentBuild', defaultValue: true, description: 'Choose the buildType.')
    }

    //Definition of env variables that can be used throughout the pipeline job
    environment {
        // Github data
        String gitUrl = "https://github.com/aleksanderderkanosov/jenkins-ci.git"
        
        // Unity tool installation
        UNITY_EXECUTABLE = "E:\\Unity\\Hub\\Editor\\2021.3.5f1\\Editor\\Unity.exe" // create system environment var pointing to unity install path

        // Unity Build params
        BUILD_NAME = "${buildTarget}-${currentBuild.number}"
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
                        git url: "${gitUrl}", branch: "${gitBranch}"
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
                    bat 'cd %outputFolder% || mkdir %outputFolder%'

                    echo "Buld App..."
                    bat '%UNITY_EXECUTABLE% -projectPath %CD% -quit -batchmode -nographics -buildTarget %buildTarget% -customBuildPath %CD%\\%outputFolder%\\ -customBuildName %BUILD_NAME% -executeMethod BuildCommand.PerformBuild'
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

