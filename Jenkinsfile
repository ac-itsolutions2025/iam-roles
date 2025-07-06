pipeline {
  agent any

  environment {
    STACK_NAME     = 'acit-eks-iam-roles'
    TEMPLATE_FILE  = 'iam-roles.yaml'   // Adjust if filename differs
    REGION         = 'us-east-2'
    ENVIRONMENT    = 'acit'                 // Used for resource naming and exports
  }

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  stages {
    stage('Checkout') {
      steps {
        echo '📦 Checking out repo'
        checkout scm
      }
    }

    stage('Deploy IAM Stack') {
      steps {
        echo '🚀 Deploying IAM stack for EKS cluster and nodegroup'
        sh '''
          aws cloudformation deploy \
            --stack-name "$STACK_NAME" \
            --template-file "$TEMPLATE_FILE" \
            --region "$REGION" \
            --capabilities CAPABILITY_NAMED_IAM \
            --parameter-overrides \
              Environment="$ENVIRONMENT"
        '''
      }
    }

    stage('Show Stack Outputs') {
      steps {
        echo '📡 EKS IAM Role Outputs:'
        sh '''
          aws cloudformation describe-stacks \
            --stack-name "$STACK_NAME" \
            --region "$REGION" \
            --query "Stacks[0].Outputs[*].[OutputKey, OutputValue]" \
            --output table
        '''
      }
    }
  }

  post {
    success {
      echo '✅ IAM stack deployed successfully.'
    }
    failure {
      echo '❌ Deployment failed. Please review the logs above.'
    }
  }
}
