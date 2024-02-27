properties([[$class: 'RebuildSettings', autoRebuild: false, rebuildDisabled: false], parameters([gitParameter(branch: '', branchFilter: '.*', defaultValue: 'master', description: 'Use any branch for qa & release branches for prod', name: 'BRANCH_NAME', quickFilterEnabled: false, selectedValue: 'NONE', sortMode: 'NONE', tagFilter: '*', type: 'PT_BRANCH_TAG'), choice(choices: ['us', 'uk', 'de'], description: 'Environment value required only in production', name: 'ENV')])])

pipeline {
    options { disableConcurrentBuilds() }
    agent {label "k8-slaves-node-18"}
    environment {
        prd_pattern = 'release'
    }
    stages {
        stage ('Setup Env') {
            steps {
                script {
                    if ( "$BRANCH_NAME" =~ env.prd_pattern ) {
                      if ("$ENV" == "us")
                      {
                        env.s3_bucket = 'tfg-snappy-prod'
                      }
                      else if ("$ENV" == "uk")
                      {
                        env.s3_bucket = 'tfg-snappy-prod-uk'
                      }
                      else if ("$ENV" == "de")
                      {
                        env.s3_bucket = 'tfg-snappy-prod-de'
                      }
                        env.awsCreds = 'aws-master-prod'
                        env.ENVIR = 'prod'
                    }
                    else 
                    {
                      env.s3_bucket = 'snappy-qa'
                      env.ENVIR = 'qa'
                      env.awsCreds = 'aws-master-dev'
                    }
                    displayVar = "${GIT_COMMIT[0..5]}-${env.ENVIR}"
                    currentBuild.displayName = "${displayVar}"
                    env.ADD_TAG = "snappy-${env.ENVIR}"
                    
                    withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')]) {

                    env.NPM_TOKEN = "${NPM_TOKEN}"

                }
                }
            }
        }

        stage('SCM'){
            steps {

                script {
                      def jobLabel = "${BRANCH_NAME}".replaceAll("[/_.]", "-").toLowerCase().take(63)
                      currentBuild.displayName = "${jobLabel}-${ENVIR}#${BUILD_NUMBER}"
                }
                
                checkout([
                $class: 'GitSCM', 
                branches: [[name: "${BRANCH_NAME}"]], 
                extensions: [], 
                userRemoteConfigs: [[credentialsId: 'snappy-bbcloud-cred', url: 'git@bitbucket.org:techstyleinc/snappy-signup.git']]
                ])
            }
        }
        
        stage('Build'){
            steps {
                ansiColor('xterm') {
                    writeFile file: '.npmrc', text: '''@fortawesome:registry=https://npm.fontawesome.com/
                        //npm.fontawesome.com/:_authToken=80D8087D-60C2-4D37-A113-67EE77374A69
                        registry=https://registry.techstyle.net/repository/npm-all/
                        //registry.techstyle.net/repository/npm-all/:_authToken=${NPM_TOKEN}
                        //registry.techstyle.net/repository/npm-all/:always-auth=true
 
                        strict-ssl=false'''
                        
                    sh '''
                    #!/bin/bash
                    set +x
                    if [ "${ENVIR}" = "prod" ]; then
                       if [ "${ENV}" = "uk" -o "${ENV}" = "de" ]; then
                          yarn install && yarn build:${ENV}
                       else
                          yarn install && yarn build:prod
                       fi
                    elif [ "${ENVIR}" = "qa" ]; then
                         yarn install && yarn build

                    fi
                    echo "JenkinsBuildID=${BUILD_NUMBER}\nGITBRANCH=${GIT_BRANCH}\nGIT_COMMIT=${GIT_COMMIT}\nBUILD_URL=${JOB_URL}" > ./buildinfo.txt
                    mkdir -p ../build
                    ls
                    cp ./buildinfo.txt ../build/
                    ##cp -r * ../build/

                    ##cp -r !(Jenkinsfile) ./build/
                    echo AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                    echo AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    '''
                }
            }
        }
        stage('Deploy'){
            steps {
                ansiColor('xterm') {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: env.awsCreds, accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        ansiColor('xterm') {

                            sh "aws s3 cp ../build s3://\"${env.s3_bucket}\" --recursive"
                        }
                    }
                }
            }
        }
    }
}
