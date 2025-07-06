pipeline {
  agent any

  environment {
    STACK_NAME     = 'acit-eks-iam'
    TEMPLATE_FILE  = 'iam-roles.yaml'
    ENVIRONMENT    = 'acit'
  }

  options {
    timestamps()
    disableConcurrentBuilds()
  }

  stages {
    stage('Checkout') {
      steps {
        echo '📦 Checking out IAM CloudFormation template'
        checkout scm
      }
    }

    stage('Deploy IAM Stack') {
      steps {
        echo '🚀 Deploying IAM resources (global service)'
        sh '''
          aws cloudformation deploy \
            --stack-name "$STACK_NAME" \
            --template-file "$TEMPLATE_FILE" \
            --capabilities CAPABILITY_NAMED_IAM \
            --parameter-overrides Environment="$ENVIRONMENT"
        '''
      }
    }

    stage('Get Stack Outputs') {
      steps {
        echo '📡 Displaying IAM resource outputs'
        sh '''
          aws cloudformation describe-stacks \
            --stack-name "$STACK_NAME" \
            --query "Stacks[0].Outputs[*].[OutputKey, OutputValue]" \
            --output table
        '''
      }
    }
  }

  post {
    success {
      echo '✅ EKS IAM stack deployed successfully!'
    }
    failure {
      echo '❌ Deployment failed. Please check the logs above.'
    }
  }
}
