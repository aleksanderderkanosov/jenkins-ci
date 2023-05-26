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
        OUTPUT_FOLDER = "Builds\\CurrentBuild-${currentBuild.number}"

        //PARAMETERS DATA
        IS_DEVELOPMENT_BUILD = "${params.developmentBuild}"
    }

    //Options: add timestamp to job logs and limiting the number of builds to be kept.
    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: "10"))
    }

    agent {
        node {
            label "Master-build-agent"
        }
    }
    //The steps necessary to generate the desired build
    stages {
        stage('Build') {
            steps {
                script {
                    platforms = ['Android', 'StandaloneWindows']
                    platforms.each { platform ->
                        if (params.buildTarget == 'All' || params.buildTarget == platform) {
                            OUTPUT_FOLDER = env.OUTPUT_FOLDER + "\\${platform}"
                            echo "OUTPUT_FOLDER: ${OUTPUT_FOLDER}"
                            bat "cd ${OUTPUT_FOLDER} || mkdir ${OUTPUT_FOLDER}"
                            bat "${UNITY_EXECUTABLE} -projectPath %CD% -quit -batchmode -nographics -buildTarget ${platform} -customBuildPath %CD%\\${OUTPUT_FOLDER}\\ -customBuildName ${BUILD_NAME} -executeMethod BuildCommand.PerformBuild"
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

