AWS_CREDENTIAL_ID = 'a74bb80d-f6cd-447c-896f-c0874df82e68'
DIGOPS_STACKS_VERSION = '0.19.0'

CUSTOMER = 'Digital Operations'
PURPOSE = 'App Mesh POC'
REGION = 'us-east-1'

TEMPLATE = 'template.yml'
ENV = env.BRANCH_NAME.split('/')[1]
STACK_NAME = 'appmesh-poc'

pipeline {
  agent {
    docker {
      label 'docker'
      image "meredithdigops/digops-stacks:$DIGOPS_STACKS_VERSION"
    }
  }

  options {
    disableConcurrentBuilds()
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }

  environment {
    AWS_ID = credentials("${AWS_CREDENTIAL_ID}")
    AWS_ACCESS_KEY_ID = "${env.AWS_ID_USR}"
    AWS_SECRET_ACCESS_KEY = "${env.AWS_ID_PSW}"
    PYTHONUNBUFFERED = "1"
  }

  stages {
    stage('Lint') {
      steps {
        sh """
          cfn-lint $TEMPLATE
        """
      }
    }

    stage('Deploy') {
      when {
        branch 'environment/*'
      }

      steps {
        sh """
          digops-stacks change-set $STACK_NAME template.yml \
            --customer '$CUSTOMER' \
            --environment '$ENV' \
            --purpose '$PURPOSE' \
            --region $REGION
        """

        sh """
          digops-stacks execute $STACK_NAME \
            --environment '$ENV' \
            --region $REGION \
            --termination-protection
        """
      }
    }
  }
}

// vim: ft=jenkinsfile.groovy
