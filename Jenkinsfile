pipeline {
  agent any
  
  environment {
    DEPLOY_CREDS = credentials('deploy-anypoint-user')
    MULE_VERSION = '4.1.5'
    BG = "Mulesoft\\Training"
    WORKER = "Micro"
    APPNAME = "devops-1-hello-world"

    DEPLOY_BAT = "true"
  }
  stages {
    stage('Build') {
      steps {
            sh 'mvn -B -U -e -V clean -DskipTests package'
      }
    }

    stage('Deploy Development') {
      environment {
        ENVIRONMENT = 'Development'
        APP_NAME = 'dev-${APPNAME}'
      }
      steps {
            sh 'mvn -U -V -e -B -DskipTests deploy -DmuleDeploy -Dmule.version=$MULE_VERSION -Danypoint.username=$DEPLOY_CREDS_USR -Danypoint.password=$DEPLOY_CREDS_PSW -Dcloudhub.app=$APP_NAME -Dcloudhub.environment=$ENVIRONMENT -Dcloudhub.bg="$BG" -Dcloudhub.worker=$WORKER -Denv.name=dev'
      }
    }

    stage('Deploy Production') {
        environment {
          ENVIRONMENT = 'Production'
          APP_NAME = '${APPNAME}'
        }
        steps {
              sh 'mvn -U -V -e -B -DskipTests deploy -DmuleDeploy -Dmule.version=$MULE_VERSION -Danypoint.username=$DEPLOY_CREDS_USR -Danypoint.password=$DEPLOY_CREDS_PSW -Dcloudhub.app=$APP_NAME -Dcloudhub.environment=$ENVIRONMENT -Dcloudhub.bg="$BG" -Dcloudhub.worker=$WORKER -Denv.name=prod'
        }
  	}
  }
  tools {
    maven 'maven'
  }
}