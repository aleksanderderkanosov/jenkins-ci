pipeline {

    //Variable inputs that modify the behavior of the job
    parameters {
        choice(name: 'buildTarget', choices: ['All', 'Android', 'StandaloneWindows'], description: "Choose the target platform.")
        string(name: 'gitBranch', defaultValue: 'master', description: 'Set the branch.')
        booleanParam(name: 'developmentBuild', defaultValue: true, description: 'Choose the buildType.')
    }

    //Definition of env variables that can be used throughout the pipeline job
    environment {
        appname = "JenkinsCi" // Set to your own game
        // Github data
        String gitUrl = "https://github.com/aleksanderderkanosov/jenkins-ci.git"
        
        // Unity tool installation
        UNITY_EXECUTABLE = "E:\\Unity\\Hub\\Editor\\2021.3.5f1\\Editor\\Unity.exe" // create system environment var pointing to unity install path

        // Unity Build params
        BUILD_NAME = "${appname}_${currentBuild.number}"
        String outputFolder = "CurrentBuild"

        //PARAMETERS DATA
        IS_DEVELOPMENT_BUILD = "${params.developmentBuild}"

        //RELEASE_NOTES = sh (script: """cd C:\\Users\\Admin\\AppData\\Local\\Jenkins\\.jenkins && git log --format="medium" -1 ${GIT_COMMIT}""", returnStdout:true)

        // Add other EnvVars here
    }

    //Options: add timestamp to job logs and limiting the number of builds to be kept.
    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: "10"))
    }

    //Tag Selector of the agent that will run the build job
    agent none
    /*agent {
        node {
            // Jenkins node to be used must have the label android
            label "android"
        }
    }*/
    //The steps necessary to generate the desired build
    stages {
        stage('Git Pull') {
            agent any
            steps {
                echo "Git pull repo."
                script {
                    try {
                        git url: "${gitUrl}", branch: "${gitBranch}"
                    } catch (e) {
                        currentBuild.result = "FAILED"
                        echo "JOB FAILED: The selected branch does not exists."
                    }
                    echo "Last commit: ${GIT_COMMIT}"
                }
            }
        }
        stage('Build') {
            matrix {
                agent {
                    label "${PLATFORM}-agent"
                }
                when { 
                    anyOf {
                        expression { params.buildTarget == 'All' }
                        expression { params.buildTarget == env.PLATFORM }
                    } 
                }
                axes {
                    axis {
                        name 'PLATFORM'
                        values 'Android', 'StandaloneWindows'
                    }
                }
                stages {
                    stage('Creating directory') {
                        steps {
                            script {
                                echo "Create Application output folder..."
                                bat 'cd %outputFolder%\\%PLATFORM% || mkdir %outputFolder%\\%PLATFORM%'
                            }
                        }
                    }
                    stage('Build') {
                        steps {
                            script {
                                echo "Do Build for ${PLATFORM}"
                                bat '%UNITY_EXECUTABLE% -projectPath %CD% -quit -batchmode -nographics -buildTarget %PLATFORM% -customBuildPath %CD%\\%outputFolder%\\%PLATFORM%\\ -customBuildName %BUILD_NAME% -executeMethod BuildCommand.PerformBuild'
                            }
                        }
                    }
                }
            }
        }
    }
    //Any action we want to perform after all the steps have succeeded or failed
    post {
        success {
            echo "Success!"
        }
        failure {
            echo "Failure!"
        }
    }
}

