pipeline {
        // Variable inputs that modify the behavior of the job
        parameters {
            booleanParam(name: 'developmentBuild', defaultValue: true, description: 'Choose the build type:')
            choice(name: 'scriptingBackend', choices: ['Mono2x', 'IL2CPP'], description: 'Pick scripting backend:')
            //choice(name: 'compressionMethod', choices: ['Default', 'Lz4', 'Lz4HC'], description: 'Pick compression method:')
        }

        // Definition of env variables that can be used throughout the pipeline job
        environment {
            // Unity build params
            BUILD_NAME = "JenkinsCi_${params.scriptingBackend}"
            //OUTPUT_FOLDER = "Builds\\CurrentBuild-${currentBuild.number}"
            //BAT_COMMAND = "${UNITY_EXECUTABLE} -projectPath %CD% -quit -batchmode -nographics -scriptingBackend ${params.scriptingBackend} "
            //BUILD_OPTIONS_ENV_VAR = "CompressWith${params.compressionMethod}"
        }

        // Options: add timestamp to job logs
        options {
            timestamps()
        }

        agent {
            node {
                label "Master-build-agent"
            }
        }

        stages {
            stage('Init Build'){
                steps {
                    script {
                        if (!currentBuild.getBuildCauses('jenkins.branch.BranchEventCause').isEmpty()) {
                            stage('Build on Push') {
                                echo "Build on Push"
                            }
                        }
                        else {
                            stage('Build on Run') {
                                echo "Build on Run"
                                //echo "${currentBuild.getBuildCauses()}"
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