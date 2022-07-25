pipeline {
    agent any
    environment {
        AWS_ACCESS_KEY_ID = credentials('AWS_Access_KeyId')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_Secret_Key')
        AWS_S3_BUCKET = "artifict-dotnet-repo"
        ARTIFACT_NAME = "ROOT.dll"
        AWS_EB_APP_NAME = "app-dotnet"
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_EB_ENVIRONMENT = "Appdotnet-env"
        // SONAR_IP = "54.226.50.200"
        // SONAR_TOKEN ="sqp_14416226962f90245e5910c676578eb661c04d62"
       
       
    }

    stages {
        stage('Restore') {
            steps {
                sh "dotnet restore"

            }
        }
         stage('Build') {
            steps {
                sh "dotnet build"
            }
        }
         stage('Test') {
            steps {
                sh "dotnet test"
            }
        }
        // stage('Quality Scan'){
        //     steps {
        //         sh '''
        //             /usr/bin/dotnet /home/ubuntu/.dotnet/tools/dotnet-sonarscanner begin /k:"dotnet-project" /d:sonar.host.url=$SONAR_IP /d:sonar.login=$SONAR_TOKEN
        //             /usr/bin/dotnet build
        //             /usr/bin/dotnet /home/ubuntu/.dotnet/tools/dotnet-sonarscanner end /d:sonar.login=$SONAR_TOKEN
        //         '''
        //     }
        // }
         stage('Publish') {
            steps {
                sh "dotnet publish"
            }
            post{
                success{
                    archiveArtifacts artifacts: '**/bin/Debug/net6.0/**.dll', followSymlinks: false

                    
                }
            }
        }
        stage('Publish artifacts to S3 Bucket') {
            steps {
                sh "aws configure set region us-east-1"
                sh "aws s3 cp ./bin/Debug/net6.0/pipelines-dotnet-core.dll s3://$AWS_S3_BUCKET/$ARTIFACT_NAME"
            }
         }
        stage('Deploy') {
            steps {
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT --version-label $AWS_EB_APP_VERSION'
            }
         }
        

    }
}


 