
import groovy.json.JsonSlurperClassic
import groovy.transform.Field

def branchSelector(){
  if (env.BITBUCKET_PAYLOAD != null){
    def jsonPayload = new JsonSlurperClassic().parseText("${env.BITBUCKET_PAYLOAD}")
    assert jsonPayload instanceof java.util.Map
    if(jsonPayload.pullRequest != null && jsonPayload.pullRequest.closed == true){
       echo "DEBUG: env.BITBUCKET_TARGET_BRANCH: ${env.BITBUCKET_TARGET_BRANCH}"
       return env.BITBUCKET_TARGET_BRANCH
    }else{
       echo "DEBUG: env.BITBUCKET_SOURCE_BRANCH: ${env.BITBUCKET_SOURCE_BRANCH}"
       return env.BITBUCKET_SOURCE_BRANCH
    }
  }else{
    if(params.BRANCH_NAME != ''){
      return params.BRANCH_NAME
    }else{
      error("Please enter BRANCH_NAME parameter or submit a PR.")
    }
  }
}

def sanitizeName(String originalName){
  saneName = originalName.replaceAll("[/_.]", "-").toLowerCase().take(33)
  if (saneName.endsWith("-")){
    saneName = saneName.substring(0, saneName.length() -1)
  }
  return saneName
}

// Jenkins Job Parameters
def sourceBranch            = ''
def applicationEnv          = params.APP_ENV
def nodeVersion             = params.NODE_ENV
def forceDelete             = params.DELETE_BRANCH
def runUnitTests            = params.RUN_UNIT_TESTS

if(params.BRANCH_NAME == ''){
  sourceBranch = env.BITBUCKET_SOURCE_BRANCH
}else{
  // just set a placeholder string, in case debugging is needed
  sourceBranch = params.BRANCH_NAME
}

currentBuild.displayName = branchSelector()+"-#"+BUILD_NUMBER

@Field config     = null
@Field devJenkins = "jenkins-k8s-dev.random.tech"
@Field prodJenkins= "jenkins-k8s.random.tech"

////some handy Groovy functions!
def getJenkinsMaster() {
 //get the Jenkins server name without the port #
 return env.BUILD_URL.split("/")[2].split(":")[0]
}

def IsRunningOnProdJenkins(){

 if(getJenkinsMaster() == prodJenkins){
   return true
 }else{
   return false
 }
}

def IsRunningOnDevJenkins(){
 //String devJenkins = "jenkins-k8s-dev.random.tech"
 if(getJenkinsMaster() == devJenkins && (
     getBranchName() ==~ /^(chore|feat(ure)?|(hot|bug)fix)\\/.*$/ ||
     getBranchName() ==~ /^(master|qa\d+|develop|release\\/.*)$/
   )
 ){
   return true
 }else{
   return false
 }
}

def isClosingPR(){
  if (env.BITBUCKET_PAYLOAD != null){
    def jsonPayload = new JsonSlurperClassic().parseText("${env.BITBUCKET_PAYLOAD}")
    assert jsonPayload instanceof java.util.Map
    if(jsonPayload.pullRequest != null && jsonPayload.pullRequest.closed == true){
      return true
    }else{
      return false
    }
  }
}


def getForceDelete(){
 return forceDelete
}

def getSafeBranchName(){
  return  sanitizeName(branchSelector())
}

def getSourceBranchSafeName(){
 return sanitizeName(BRANCH_NAME)
}

def getBranchName(){
 return branchSelector()
}

def getNumberOfNonProdReplicas() {
 return 1
}

def getNumberOfProdReplicas() {
 return 5
}

def getRequestedNonProdCPU(){
 return "500m"
}

def getRequestedNonProdMemory(){
 return "500Mi"
}

def getLimitNonProdCPU(){
 return "500m"
}

def getLimitNonProdMemory(){
return "500Mi"
}

def getRequestedProdCPU(){
 return "125m"
}

def getRequestedProdMemory(){
 return "500Mi"
 }

def getLimitProdCPU(){
 return "400m"
}

def getLimitProdMemory(){
 return "1024Mi"
}

def getAppEnv(){
 return params.APP_ENV
}

def getServiceName(){
 //return a compatible service name and truncate
 safeServiceName = "qa-tier1-random-"+getSafeBranchName()+"-qa-tier1-random"
 safeServiceName = safeServiceName.replaceAll("[/_.]", "-").toLowerCase().take(34)
 if (safeServiceName.endsWith("-")) {
   safeServiceName = safeServiceName.take(33)
 }
 return safeServiceName
}

def getSourceServiceName(){
 safeSourceServiceName =  "qa-tier1-random-"+sanitizeName(BRANCH_NAME)+"-qa-tier1-random"
 safeSourceServiceName = safeSourceServiceName.replaceAll("[/_.]", "-").toLowerCase().take(34)
 if (safeSourceServiceName.endsWith("-")) {
   safeSourceServiceName = safeSourceServiceName.take(33)
 }
 return safeSourceServiceName
}

def getIngressHost(){
 env.HTTP_ENDPOINT= "qa-tier1-random-"+getSafeBranchName()+"-qa-tier1-random"+".random.tech"
 return "qa-tier1-random-"+getSafeBranchName()+"-qa-tier1-random"
}

def getTier1IngressHost(){
 env.TIER1_HTTP_ENDPOINT= "tier1-"+getSafeBranchName()+".random-tier1-qa.random.tech"
 return "tier1-"+getSafeBranchName()
}

def getIngressIp(){
 return "10.50.4.132"
}

def getProjectName(){
 return "random-tier1-qa"
}

def getServicePort(){
 return "42051"
}

def getServiceHome(){
 return "/tier1-random"
}

def getNodeEnv(){
 return "production"
}

def getNodeConfigEnv(){
 return "qa"
}

def getEnvBuild(){
 return "qa"
}

def getEnvment(){
 return "qa1"
}

def getXServiceTokenCred(){
 return "tier-1-service-token-qa"  // TODO: need to add creds value in jenkins
}

def getSentryAuthToken(){
 return "random-tier1-sentry-token-qa"
}

def sentryTracesSampleRate(){
 return "0"
}

def sentrySampleRate(){
 return "0"
}

def sentryOrg(){
 return "randomrands"
}

def sentryProject(){
 return "random-tier-1"
}

def sentryDNS(){
 return "https://e1a0af792d214da4a7ac2dfcc55cc6a7@o1169287.ingest.sentry.io/4505443624681472"
}

def getHttps(){
 return "false"
}

def getServiceHealthcheck(){
 return "/ping"
}

def getDnsUser(){
 return  "svc_ibx_k8s"
}

def getDnsApiUrl(){
 return "https://randomelsvmlxibx01.corp.brandideas.com/wapi/v2.12"
}

def getRegistryUser(){
 return "nexus-user-creds"
}

def getHost(){
 return "0.0.0.0"
}

def getBentoEnvField() {
 def BRANCH_NAME = getBranchName()
 def user = sh(
   returnStdout: true,
   script: "curl -s -H \"APIKey: ba6337f4d29ce58378d9a25cf643be83\" https://secrets.random.net/api/passwords/4658 | jq -r '.[][\"UserName\"]' | tr -d '[:space:]'"
 )
 def pass = sh(
   returnStdout: true,
   script: "curl -s -H \"APIKey: ba6337f4d29ce58378d9a25cf643be83\" https://secrets.random.net/api/passwords/4658 | jq -r '.[][\"Password\"]' | tr -d '[:space:]'"
 )
 def JIRA_TICKET_NO = sh(
   returnStdout: true,
   script: "echo \"$BRANCH_NAME\" |  tr '/' '-' | tr '_' '-' |  tr '.' '-' | awk -F'-' '{print \$2\"-\"\$3}' | tr '[:lower:]' '[:upper:]' | tr -d '[:space:]'"
 )
 def BENTO_ENV_FIELD = sh(
   returnStdout: true,
   script: "curl -u \"$user:$pass\" https://jira.random.net/rest/api/2/issue/\"$JIRA_TICKET_NO\"/?fields\\=customfield_18300 | jq -r '.fields' | jq -r '.customfield_18300' | tr -d ' '"
 )
 echo "bento env field: $BENTO_ENV_FIELD"
 if (BENTO_ENV_FIELD != "" && BENTO_ENV_FIELD != null && BENTO_ENV_FIELD != "null") {
   return BENTO_ENV_FIELD
 }

 return null
}

def sendSlackNotification(String branch, 
 String deploymentUrl, 
 String buildNumber, 
 String buildUrl,
 boolean successful,
 boolean covFailed) {
 
 branchName = branch.toLowerCase()
 urlPrefix = getServiceName()
 if (successful && urlPrefix ==~ /^(qa[1-3])-tier1$/) {
   gatewayUrl = "${urlPrefix}.randomfashionos.com/api/status"
   slackSend(
     channel: '#random-qa-deploys',
     message: "Tier1 random K8s Deployment Successful! - #${buildNumber} (<${buildUrl}|Open>)\nTIER1 QA Deployment\nGIT REPO: https://git.random.net/scm/tier/tier1-random.git\nBRANCH: ${branchName}\nURL: <${deploymentUrl}|${deploymentUrl}>\nVia Gateway: <${gatewayUrl}|${gatewayUrl}>", 
     color: 'good')
       
 } else if (successful) {
   slackSend(
     channel: '#random-qa-deploys',
     message: "Tier1 random K8s Deployment Successful! - #${buildNumber} (<${buildUrl}|Open>)\nTIER1 QA Deployment\nGIT REPO: https://git.random.net/scm/tier/tier1-random.git\nBRANCH: ${branchName}\nURL: <${deploymentUrl}|${deploymentUrl}>", 
     color: 'good')
 } else if (covFailed && urlPrefix ==~ /^(qa[1-3])-tier1$/) { 
   gatewayUrl = "${urlPrefix}.randomfashionos.com/api/status"
   slackSend(
     channel: '#random-qa-deploys',
     message: "Tier1 random K8s Deployment Successful But Coverage Failed! - #${buildNumber} (<${buildUrl}|Open>)\nTIER1 QA Deployment\nGIT REPO: https://git.random.net/scm/tier/tier1-random.git\nBRANCH: ${branchName}\nURL: <${deploymentUrl}|${deploymentUrl}>\nVia Gateway: <${gatewayUrl}|${gatewayUrl}>", 
     color: 'warning')
 } else if (covFailed) {
   slackSend(
     channel: '#random-qa-deploys',
     message: "Tier1 random K8s Deployment Succesful But Coverage Failed! - #${buildNumber} (<${buildUrl}|Open>)\nTIER1 QA Deployment\nGIT REPO: https://git.random.net/scm/tier/tier1-random.git\nBRANCH: ${branchName}\nURL: <${deploymentUrl}|${deploymentUrl}>", 
     color: 'warning')
 } else {
   slackSend(
     channel: '#random-qa-deploys',
     message: "Tier1 random K8s Deployment Failed! - #${buildNumber} (<${buildUrl}|Open>)\nTIER1 QA Deployment\nGIT REPO: https://git.random.net/scm/tier/tier1-random.git\nBRANCH: ${branchName}\nURL: <${deploymentUrl}|${deploymentUrl}>", 
     color: 'danger')
 }
}

def notifyBitbucket(String state, String COMMIT_HASH) {
 
    if('SUCCESS' == state || 'FAILED' == state) {
    // Set result of currentBuild !Important!
        currentBuild.result = state
    }

    notifyBitbucket commitSha1: COMMIT_HASH, 
      considerUnstableAsSuccess: false, 
      credentialsId: 'b341dde8-f5c7-453a-a25b-9aca7d2c0830', 
      disableInprogressNotification: false, 
      ignoreUnverifiedSSLPeer: false, 
      includeBuildNumberInKey: false, 
      prependParentProjectKey: false, 
      projectKey: '', 
      stashServerBaseUrl: 'https://git.random.net'
}

pipeline{
  agent {
    label  "k8-slaves-node-$nodeVersion"
  }

  options {
    //discard after number of specified builds
    buildDiscarder(logRotator(daysToKeepStr: '60', numToKeepStr: '200'))
    timeout(time: 60, unit: 'MINUTES')
    skipDefaultCheckout()
  }

  environment {
    
    BRANCH_NAME                 = branchSelector()

    SERVICE_REGISTRY            = "$applicationEnv/devops-test/tier1-random"
    DOCKER_REGISTRY             = "snpm-docker-" + "$applicationEnv" + ".random.net/"
    DOCKER_IMAGE_NAME           = "$DOCKER_REGISTRY"+"$SERVICE_REGISTRY"
    DOCKER_REGISTRY_PATH        = "https://" + "${env.DOCKER_REGISTRY}" + "${env.service_registry}"
   // // docker HTTP API endpoint requires /v2/
   // DOCKER_HTTP_API_URL       = "https://" + "${env.DOCKER_REGISTRY}" + "v2/" + "${env.service_registry}"
   // // we decided to use the branch name as image tag. tags can be longer, but
   // // we should limit tag size 63 chars, as it's also used as DNS subdomain
    DOCKER_IMAGE_TAG		        = getSafeBranchName()
    REGISTRY_USER               = getRegistryUser()
    runUnitTests                = "$runUnitTests"
    NODE_VERSION                = "$nodeVersion" //readFile('.nvmrc').trim() 
    NPM_TOKEN = sh(
      returnStdout: true,
      script: "curl -s -H \"APIKey: bec693d854af2739b3a24fe685dc377b\" https://secrets.random.net/api/passwords/3572  | jq -r '.[][\"Password\"]'"
    )

    // skip to delete option
    forceDelete = "$forceDelete"
 }

  stages{
    stage("Checkout"){
      when {
        expression { forceDelete != true }
      }
      steps{
        script {
          env.COMMIT_HASH = checkout([
            $class: 'GitSCM',
            branches: [[name: "$BRANCH_NAME"]],
            userRemoteConfigs: scm.userRemoteConfigs
          ]).GIT_COMMIT
          echo "COMMIT HASH: ${env.COMMIT_HASH}"
          notifyBitbucket('INPROGRESS', env.COMMIT_HASH)
        }
      }
    }

    stage('Build Env') {
      when {
        expression { forceDelete != true }
      }

      steps {

        writeFile file: '.npmrc', text: '''registry=https://registry.random.net/repository/npm-all/
                  //registry.random.net/repository/npm-all/:_authToken=${NPM_TOKEN}
                  //registry.random.net/repository/npm-all/:always-auth=true

                  strict-ssl=false'''

        sh '''
        env
        npm config list -l
        git --version
        node -v
        npm -v
        '''
        withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')]) {
        sh 'npm i -g dotenv-cli'
        sh 'npm ci'
      }
      }
    }
    
    stage('Analysis') {
      when {
        // Skip when there's nothing new to test
        expression { forceDelete != true }
        not { expression { isClosingPR() } }
      }

      environment {
        // Write JUnit report to coverage directory so it will get included in the saved artifact
        JEST_JUNIT_OUTPUT_DIR = 'coverage'
      }

      parallel {
        stage('Unit Tests') {
          when {
            expression { runUnitTests == true }
          }
          steps {
            withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')]) {
            sh 'dotenv -e env/test.env -- npm run test:ci:sync'
          }
          }
        }

        // TODO: merge this with Test stage some day
        stage('Coverage') {
          when {
            expression { runUnitTests == true }
          }
          steps {
            echo '(TEMPORARY) coverage check will be part of Unit Test stage in the future'
            // TODO: change this to a plain `sh` command some day, to fail builds when coverage fails
            script {
              try {
                withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')]) {
                sh('dotenv -e env/test.env -- npm run test:ci:cov')
              }
              } catch (error) {
                // NOTE: prevent build from failing on coverage failures, just mark it unstable (TEMPORARY soft-fail)
                currentBuild.description = '⚠️ MINIMUM COVERAGE REQUIREMENTS NOT MET — PLEASE UPDATE TESTS'
                currentBuild.result = 'UNSTABLE'
              }
            }
          }
        }

        // TODO: enable this stage (with soft fail or hard fail?) some day
        // stage('Lint') {
        //   steps {
        //     echo 'Checking for lint errors (to validate that pre-commit hook was not bypassed)'
        //     sh 'npm run lint'
        //   }
        // }

        stage('Swagger') {
          steps {
            withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')]) {
            sh 'npm run swagger'
          }
          }
        }
      }
      post {
        always {
          script {
              if ( runUnitTests == true)
                // Parse JUnit report
                junit '**/junit.xml'
                // Save coverage artifacts for this build
                archiveArtifacts '**/coverage/**/*'
          }
        }
      }
    }

    stage('Build App') {
      when { expression { forceDelete != true } }

      steps {
        withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN'), ]) {
        sh 'npm run build'
      }
      }
    }

    stage('Build Sentry') {
      when { expression { forceDelete != true } }

      environment {
        sentryAuthToken               = getSentryAuthToken()
        SENTRY_AUTH_TOKEN             = credentials("${env.sentryAuthToken}")
        SENTRY_TRACES_SAMPLE_RATE     = sentryTracesSampleRate()
        SENTRY_SAMPLE_RATE            = sentrySampleRate()
        SENTRY_DNS                    = sentryDNS()
        SENTRY_ORG                    = sentryOrg()
        SENTRY_PROJECT                = sentryProject()
        SENTRY_RELEASE               = "${env.COMMIT_HASH}"
      }

      steps {
        sh './node_modules/@sentry/cli/bin/sentry-cli sourcemaps inject ./dist'
        sh './node_modules/@sentry/cli/bin/sentry-cli sourcemaps upload --org="${SENTRY_ORG}" --project="${SENTRY_PROJECT}" --release="${SENTRY_RELEASE}" ./dist'
      }
    }

    stage("Build Docker image"){
      when { expression { forceDelete != true } }

      steps{
        script{
          withCredentials([string(credentialsId: 'NPM_TOKEN', variable: 'NPM_TOKEN')]) {
            docker.withRegistry('https://snpm-docker.random.net', "${env.REGISTRY_USER}"){
            app = docker.build(DOCKER_IMAGE_NAME, "--build-arg NODE_VERSION=${env.NODE_VERSION} .")
            }
          }
        }
      }
    }
    stage("Push Docker image to Nexus registry"){
      when { expression { forceDelete != true } }

      steps{
        script{
          docker.withRegistry(DOCKER_REGISTRY_PATH, "${env.REGISTRY_USER}"){
          app.push("${env.DOCKER_IMAGE_TAG}")
          app.push("latest")
          }
        }
      }
    }    
   stage("Kubernetes Deploy/Delete") {
        environment {
          APP_ENV                   = getAppEnv()
          // NPM_TOKEN and X_SERVICE_TOKEN are fake in Jenkins creds until I'm happy with the pipeline logic
          // NPM_TOKEN was in original SavageX-SPA-QA-Auto, but is not used at all. it might be used for future jobs
          // NPM_TOKEN                 = credentials('savagex-npm-token')
          xServiceTokenCred         = getXServiceTokenCred()
          X_SERVICE_TOKEN           = credentials("${env.xServiceTokenCred}")
          sentryAuthToken               = getSentryAuthToken()
          SENTRY_AUTH_TOKEN               = credentials("${env.sentryAuthToken}")
          SENTRY_TRACES_SAMPLE_RATE     = sentryTracesSampleRate()
          SENTRY_SAMPLE_RATE            = sentrySampleRate()
          SENTRY_DNS                    = sentryDNS()
          SENTRY_ORG                    = sentryOrg()
          SENTRY_PROJECT                = sentryProject()
          SENTRY_RELEASE               = "${env.COMMIT_HASH}"
          NODE_CONFIG_ENV           = getNodeConfigEnv()
          // apparently this env var is lowercase in the SavageX app
          envbuild                  = getEnvBuild()
          envment                   = getEnvment()
          HTTPS                     = getHttps()
          HOST                      = getHost()
          SERVICE_HEALTH_CHECK      = getServiceHealthcheck()
          DNS_USER                  = getDnsUser()
          DNS_API_URL               = getDnsApiUrl()

          //domains
          DOMAIN  = ".random.tech"
          TIER1_DOMAIN = ".random-tier1-qa.random.tech"

          SERVICE_NAME              = getServiceName()
          INGRESS_HOST              = getIngressHost()
          TIER1_INGRESS_HOST        = getTier1IngressHost()
          INGRESS_FQDN              = "${env.INGRESS_HOST}" + "${env.DOMAIN}"
          TIER1_INGRESS_FQDN        = "${env.TIER1_INGRESS_HOST}" + "${env.TIER1_DOMAIN}"
          INGRESS_IP                = getIngressIp()
          PROJECT_NAME              = getProjectName()
          CONFIG_MAP_NAME           = getSafeBranchName()
          SOURCE_BRANCH             = "${env.BITBUCKET_SOURCE_BRANCH}"
          SERVICE_PORT              = getServicePort()
          SERVICE_HOME              = getServiceHome()
          NODE_ENV                  = getNodeEnv()

          NUM_OF_REPLICAS_PROD      = getNumberOfProdReplicas()

          REQUESTED_CPU_PROD        = getRequestedProdCPU()
          REQUESTED_MEMORY_PROD     = getRequestedProdMemory()
          LIMIT_CPU_PROD            = getLimitProdCPU()
          LIMIT_MEMORY_PROD         = getLimitProdMemory()

          JENKINS_MASTER            = getJenkinsMaster()

          // resources for cleanup, which must only be from SOURCE branch.
          // we don't want to clean up the branch we merge to (TARGET).
          // these variables must match exactly what is in deployment/kube.yaml 
          // during the deployment stage, when a PR is open.
          SOURCE_BRANCH_CONFIG_MAP  = getSourceBranchSafeName()
          SOURCE_BRANCH_SERVICE     = getSourceServiceName()

          // QA Only
          BORDERFREE_JWT_SECRET = sh(
            returnStdout: true,
            script: "curl -s -H \"APIKey: fd8762ea53c349b8ad69e5c37f432b9f\" https://secrets.random.net/api/passwords/1188 | jq -r '.[][\"Password\"]'"
          )


          JWT_SECRET = sh(
            returnStdout: true,
            script: "curl -s -H \"APIKey: cdab497328f653ead783b6e39c52f446\" https://secrets.random.net/api/passwords/1207 | jq -r '.[][\"Password\"]'"
          )


          REDIS_PWD = sh(
            returnStdout: true,
            script: "curl -s -H \"APIKey: 86437eb23fcd5a9e684cf35db3a729e4\" https://secrets.random.net/api/passwords/1202 | jq -r '.[][\"Password\"]'"
          )

          BENTO_ENV_FIELD = getBentoEnvField()

          //for HorizontalPodAutoscaler
          CPU_PERCENT           = 80
          MIN                   = 4
          MAX                   = 6

        }

    stages {
       stage("Create DNS record"){
         when {
           expression {
             return (
                     (forceDelete != true && IsRunningOnDevJenkins() && true )
                   )
           }
         }

         steps{
           script{
             echo "Checking if DNS record exists: ${INGRESS_FQDN}"
             def queryResponse = httpRequest authentication: "${DNS_USER}", \
             ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
             contentType: 'APPLICATION_JSON', httpMode: 'GET', \
             url: "${DNS_API_URL}"+"/record:a?name=${INGRESS_FQDN}", \
             validResponseCodes: '200,301:308'
             def jsonResponse = new JsonSlurperClassic().parseText(queryResponse.content)
             //echo "DEBUG: queryResponse: ${queryResponse}"
             //echo "DEBUG: jsonResponse: ${jsonResponse}"
             assert jsonResponse instanceof List
             if(jsonResponse.size() == 1){
               def aRecord = jsonResponse.get(0)
               assert aRecord instanceof java.util.Map
               assert aRecord.get('name') == "${INGRESS_FQDN}"
               echo "DNS record exists. Moving to next stage."
               assert true
             }else{
               echo "Creating new A record. FQDN: ${INGRESS_FQDN} IP: ${INGRESS_IP}"
               def httpPayload = """
                   {"ipv4addr": "${INGRESS_IP}", "name": "${INGRESS_FQDN}"}
               """
               def response = httpRequest authentication: "${DNS_USER}", \
               ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
               contentType: 'APPLICATION_JSON', httpMode: 'POST', \
               requestBody: httpPayload, url: "${DNS_API_URL}"+"/record:a", \
               validResponseCodes: '201,301:308'
             }
           }
         }
       }
       stage("Create TIER1 DNS record"){
         when {
           expression {
             return (
                     (forceDelete != true && IsRunningOnDevJenkins() && true )
                   )
           }
         }

         steps{
           script{
             echo "Checking if DNS record exists: ${TIER1_INGRESS_FQDN}"
             def queryResponse = httpRequest authentication: "${DNS_USER}", \
             ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
             contentType: 'APPLICATION_JSON', httpMode: 'GET', \
             url: "${DNS_API_URL}"+"/record:a?name=${TIER1_INGRESS_FQDN}", \
             validResponseCodes: '200,301:308'
             def jsonResponse = new JsonSlurperClassic().parseText(queryResponse.content)
             //echo "DEBUG: queryResponse: ${queryResponse}"
             //echo "DEBUG: jsonResponse: ${jsonResponse}"
             assert jsonResponse instanceof List
             if(jsonResponse.size() == 1){
               def aRecord = jsonResponse.get(0)
               assert aRecord instanceof java.util.Map
               assert aRecord.get('name') == "${TIER1_INGRESS_FQDN}"
               echo "DNS record exists. Moving to next stage."
               assert true
             }else{
               echo "Creating new A record. FQDN: ${TIER1_INGRESS_FQDN} IP: ${INGRESS_IP}"
               def httpPayload = """
                   {"ipv4addr": "${INGRESS_IP}", "name": "${TIER1_INGRESS_FQDN}"}
               """
               def response = httpRequest authentication: "${DNS_USER}", \
               ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
               contentType: 'APPLICATION_JSON', httpMode: 'POST', \
               requestBody: httpPayload, url: "${DNS_API_URL}"+"/record:a", \
               validResponseCodes: '201,301:308'
             }
           }
         }
       }
       stage("Deploy on Dev"){
         when {
           expression {
             return (
                     (forceDelete != true && IsRunningOnDevJenkins())
                   )
           }
         }
         
         environment {
          NAMESPACE         = "${env.PROJECT_NAME}"
          NUM_OF_REPLICAS   = 1
          REQUESTED_CPU     = getRequestedNonProdCPU()
          REQUESTED_MEMORY  = getRequestedNonProdMemory()
          LIMIT_CPU         = getLimitNonProdCPU()
          LIMIT_MEMORY      = getLimitNonProdMemory()
        }
          steps{
           echo "Deploy on dev"
           
           kubernetesDeploy(
             kubeconfigId: "konvoy-staging",
             configs: "k8s/resources/deployment/kube.yaml",
             enableConfigSubstitution: true
           )
         }
         post {
           success {
             script {
               notifyBitbucket('SUCCESS', env.COMMIT_HASH)
               sendSlackNotification(getBranchName(), 
                 "https://"+env.TIER1_INGRESS_FQDN+"/status", 
                 env.BUILD_NUMBER,
                 env.BUILD_URL,
                 true,
                 false)
             }
           }
           unstable {
              script {
               notifyBitbucket('FAILED', env.COMMIT_HASH)
               sendSlackNotification(getBranchName(), 
                 "https://"+env.TIER1_INGRESS_FQDN+"/status", 
                 env.BUILD_NUMBER,
                 env.BUILD_URL,
                 false,
                 true)
             }
           }
           failure {
             script {
               notifyBitbucket('FAILED', env.COMMIT_HASH)
               sendSlackNotification(getBranchName(), 
                 "https://"+env.INGRESS_FQDN+"/status", 
                 env.BUILD_NUMBER,
                 env.BUILD_URL,
                 false,
                 false)
              }
            }
          }
        }
       stage("Cleanup - delete k8s resources, DNS record, and docker image"){
         when {
           expression {
             return (
                     ((forceDelete == true && IsRunningOnDevJenkins()) ||
                       (isClosingPR() && IsRunningOnDevJenkins()))
                   )
                   //used (IsRunningOnDevJenkins()) for testing without PR
           }
         }

         environment {
           NAMESPACE = "${env.PROJECT_NAME}"
           REGISTRY_AUTH = credentials("${env.REGISTRY_USER}")
           IMAGE_TAG_TO_DELETE = getSourceBranchSafeName()
         }

         steps{
           echo "Deleting resources from k8s"
           //echo "Waiting for deployment in previous stage to complete..."
           //sleep(time:300,unit:"SECONDS")

           kubernetesDeploy(
             kubeconfigId: "konvoy-staging",
             configs: "k8s/resources/cleanup/kube-cleanup.yaml",
             enableConfigSubstitution: true,
             deleteResource: true
           )

           script{
             echo "Cleaning up Tier1 Ingress Record"

             echo "Checking if DNS record exists: ${TIER1_INGRESS_FQDN}"
             def queryResponse = httpRequest authentication: "${DNS_USER}", \
             ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
             contentType: 'APPLICATION_JSON', httpMode: 'GET', \
             url: "${DNS_API_URL}"+"/record:a?name=${TIER1_INGRESS_FQDN}", \
             validResponseCodes: '200,301:308'
             def jsonResponse = new JsonSlurperClassic().parseText(queryResponse.content)
             echo "DEBUG: queryResponse: ${queryResponse}"
             echo "DEBUG: jsonResponse: ${jsonResponse}"
             assert jsonResponse instanceof List
             if(jsonResponse.size() == 1){
               def aRecord = jsonResponse.get(0)
               assert aRecord instanceof java.util.Map
               assert aRecord.get('name') == "${TIER1_INGRESS_FQDN}"
               echo "DNS record exists. Deleting A record. FQDN: ${TIER1_INGRESS_FQDN} IP: ${INGRESS_IP}"
               def recordRef = aRecord.get('_ref')
               def response = httpRequest authentication: "${DNS_USER}", \
               ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
               contentType: 'APPLICATION_JSON', httpMode: 'DELETE', \
               url: "${DNS_API_URL}"+"/"+"${recordRef}", validResponseCodes: '200,301:308'
               echo "DEBUG: DNS API response after DELETE call: ${response}"
             }else{
               error("Unexpected response length from DNS API. Aborting.")
             }
           }

           script{

             echo "Checking if DNS record exists: ${INGRESS_FQDN}"
             def queryResponse = httpRequest authentication: "${DNS_USER}", \
             ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
             contentType: 'APPLICATION_JSON', httpMode: 'GET', \
             url: "${DNS_API_URL}"+"/record:a?name=${INGRESS_FQDN}", \
             validResponseCodes: '200,301:308'
             def jsonResponse = new JsonSlurperClassic().parseText(queryResponse.content)
             echo "DEBUG: queryResponse: ${queryResponse}"
             echo "DEBUG: jsonResponse: ${jsonResponse}"
             assert jsonResponse instanceof List
             if(jsonResponse.size() == 1){
               def aRecord = jsonResponse.get(0)
               assert aRecord instanceof java.util.Map
               assert aRecord.get('name') == "${INGRESS_FQDN}"
               echo "DNS record exists. Deleting A record. FQDN: ${INGRESS_FQDN} IP: ${INGRESS_IP}"
               def recordRef = aRecord.get('_ref')
               def response = httpRequest authentication: "${DNS_USER}", \
               ignoreSslErrors: true, acceptType: 'APPLICATION_JSON', \
               contentType: 'APPLICATION_JSON', httpMode: 'DELETE', \
               url: "${DNS_API_URL}"+"/"+"${recordRef}", validResponseCodes: '200,301:308'
               echo "DEBUG: DNS API response after DELETE call: ${response}"
             }else{
               error("Unexpected response length from DNS API. Aborting.")
             }

             echo "Deleting image from registry"

             // query docker registry to retrieve image digest from tag using plain groovy.
             // we cannot use the HTTP Request plugin here, because the wonderful docker registry
             // API stashes the image digest in the response headers and the plugin can't handle that
             def registryReqGet = new URL("${env.DOCKER_HTTP_API_URL}" + "/manifests/" + "${IMAGE_TAG_TO_DELETE}")
             echo "Querying docker registry to get image digest for tag"
             HttpURLConnection queryConn = (HttpURLConnection) registryReqGet.openConnection()
             queryConn.setRequestMethod("GET")
             // content type "application/vnd.docker.distribution.manifest.v2+json" is important:
             queryConn.setRequestProperty("Accept", "application/vnd.docker.distribution.manifest.v2+json");
             queryConn.connect()
             def responseHeaders = queryConn.getHeaderFields()
             def queryConnResp = queryConn.getResponseCode() 
             if(queryConnResp == 200){
               // good query response. HTTP DELETE call to delete image manifest digest
               assert responseHeaders instanceof java.util.Map
               echo "DEBUG: responseHeaders ${responseHeaders}"
               // this is where we find the manifest digest (response headers):
               List dockerContentDigest = responseHeaders["Docker-Content-Digest"]
               def imageDigest = dockerContentDigest[0]
               echo "DEBUG: imageDigest: ${imageDigest}"
               // we need to base64-encode creds manually for basic auth
               def regBasicAuth = "Basic ${REGISTRY_AUTH.bytes.encodeBase64().toString()}"
               echo "DEBUG: DELETE URL: ${env.DOCKER_HTTP_API_URL} + '/manifests/' + ${imageDigest}"
               def registryReqDel = new URL("${env.DOCKER_HTTP_API_URL}" + "/manifests/" + "${imageDigest}")
               HttpURLConnection delConn = (HttpURLConnection) registryReqDel.openConnection()
               delConn.setRequestMethod("DELETE")
               delConn.setRequestProperty("Accept", "application/vnd.docker.distribution.manifest.v2+json");
               delConn.setRequestProperty("Authorization", regBasicAuth);
               delConn.connect()
               def delConnResponse = delConn.getResponseCode()
               echo "DEBUG: delConnResponse: ${delConnResponse}"
               if(delConnResponse == 202){
                 echo "Deleted image digest: ${imageDigest}"
                 //def delConnBody = delConn.getInputStream().getText()
                 //echo "DEBUG: delConnBody: ${delConnBody}"
               }else{
                 //error("Bad HTTP response from docker registry when deleting image digest: ${delConnResponse}")
                 echo "Bad HTTP response from docker registry when deleting image digest: ${delConnResponse}"
               }
               }else{
               //error("Bad HTTP response from docker registry when querying image tag: ${queryConnResp}")
               echo "Bad HTTP response from docker registry when querying image tag: ${queryConnResp}"
              }


             /*
             HTTP plugin does not work

             echo "Deleting image from registry"
             def registryGetResponse = httpRequest authentication: "${env.REGISTRY_USER}", ignoreSslErrors: true, \
             customHeaders: [[ name: 'Accept:', value: '.docker.distribution.manifest.v2+json']], httpMode: 'GET', \
             url: "https://registry.random.net/repository/docker-hosted-qa/v2/qa/devops-test/savagex/manifests/86"
             def registryHeadResponse = httpRequest authentication: "${env.REGISTRY_USER}", ignoreSslErrors: true, \
             customHeaders: [[ name: 'Accept:', value: '.docker.distribution.manifest.v2+json']], httpMode: 'HEAD', \
             url: "https://registry.random.net/repository/docker-hosted-qa/v2/qa/devops-test/savagex/manifests/60"
             echo "DEBUG: registryGetResponse: ${registryGetResponse.content}"
             echo "DEBUG: registryHeadResponse: ${registryHeadResponse.content}"
             */
            }
          }
        }
      }
    }
  }
}









