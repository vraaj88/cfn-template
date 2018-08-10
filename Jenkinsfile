
pipeline {
    agent any
    parameters{
        choice(
            choices: 'create\nupdate\nrollback',
            description: '',
            name: 'ACTION')
    }
    stages {
        stage ('checkout'){
            steps {
                git branch: '$BRANCH_NAME', changelog: false, credentialsId: '9440d5c3-c608-4ef7-9686-1b9b9d650495', poll: false, url: 'https://github.com/vraaj88/cfn-template.git' 
            }    
        }
    
        stage ('Configure AWS'){
            steps {
                sh 'sudo yum install python-pip -y'
                sh 'sudo yum install awscli -y'
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'rvemula', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) 
                {
                sh '''aws --version
                    aws configure set default.region us-east-1
                    aws configure set aws_access_key_id ${AWS_ACCESS_KEY_ID}
                    export aws_access_key=${AWS_ACCESS_KEY_ID}
                    aws configure set aws_secret_access_key ${AWS_SECRET_ACCESS_KEY}
                    export aws_secret_key=${AWS_SECRET_ACCESS_KEY}
                    '''
                }
            }

        }
        stage ('Validate template'){
            steps {
                sh 'aws cloudformation validate-template --template-body file://sample-cfn.yml'
            }
        }
   
        stage ('Create Stack') {
                    when {
                        
                        expression { params.ACTION == 'create' }
                    }
                    steps {
                        echo "Create Stack "
                        sh 'aws cloudformation create-stack --stack-name myteststack --template-body file://sampletemplate.json --parameters ParameterKey=KeyPairName,ParameterValue=TestKey ParameterKey=SubnetIDs,ParameterValue=SubnetID1\\,SubnetID2'
                    }
        }
        stage ('Update Stack') {
                    when {
                        
                        expression { params.ACTION == 'update' }
                    }
                    steps {
                        echo "Update"
                        sh ''
                    }
        }
        stage ('Rollback Stack') {
                    when {
                        
                        expression { params.ACTION == 'rollback' }
                    }
                    steps {
                        echo "rollback"
                    }
        }
        
    }


    
}
