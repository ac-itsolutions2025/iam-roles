pipeline {
  agent any

  environment {
    STACK_NAME     = 'acit-eks-iam'
    TEMPLATE_FILE  = 'iam-roles.yaml'  // Adjust if file is named differently
    ENVIRONMENT    = 'acit'                // Used in naming + export values
  }

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  stages {
    stage('Checkout') {
      steps {
        echo '📦 Cloning IAM stack template repo'
        checkout scm
      }
    }

    stage('Deploy IAM Roles') {
      steps {
        echo '🚀 Deploying IAM roles and instance profile (no tags)'
        sh '''
          aws cloudformation deploy \
            --stack-name "$STACK_NAME" \
            --template-file "$TEMPLATE_FILE" \
            --capabilities CAPABILITY_NAMED_IAM \
            --parameter-overrides Environment="$ENVIRONMENT"
        '''
      }
    }

    stage('Print IAM Stack Outputs') {
      steps {
        echo '📡 Retrieving IAM output values'
        sh '''
          aws cloudformation describe-stacks \
            --stack-name "$STACK_NAME" \
            --query "Stacks[0].Outputs[*].[OutputKey,OutputValue]" \
            --output table
        '''
      }
    }
  }

  post {
    success {
      echo '✅ IAM resources deployed successfully.'
    }
    failure {
      echo '❌ Deployment failed. Review logs above for details.'
    }
  }
}
