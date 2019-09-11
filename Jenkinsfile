pipeline {
  agent any
  
  environment {
    DEPLOY_CREDS = credentials('deploy-anypoint-user')
    MULE_VERSION = '4.1.5'
    BG = "1Platform\\Public\\CI-CD Demo"
    WORKER = "Micro"
    APPNAME = "tech-talk-mobile-customer-api"

    DEPLOY_BAT = "true"
  }
  stages {
    stage('Build') {
      steps {
            sh 'mvn -B -U -e -V clean -DskipTests package'
      }
    }

    stage('Test') {
      steps {
	      withMaven(
          mavenSettingsConfig: 'public-maven-config.xml') {
            sh "mvn -B -Dmule.env=dev test"
          }
      }
      post {
        always {
          publishHTML (target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'target/site/munit/coverage',
                        reportFiles: 'summary.html',
                        reportName: "Code coverage"
                    ]
                  )
        }
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
  post {
      always {
       step([$class: 'hudson.plugins.chucknorris.CordellWalkerRecorder'])
      }
  }
  tools {
    maven 'M3'
  }
}