pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
        string(name: 'AWS_ACCESS_KEY_ID', description: 'AWS_ACCESS_KEY_ID 1?')
        string(name: 'AWS_SECRET_ACCESS_KEY', description: 'AWS_SECRET_ACCESS_KEY 2?')
        choice(name: 'action', choices: ['apply', 'destroy'], description: '')
    } 

    environment {
        // AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        // AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_ACCESS_KEY_ID     = "${params.AWS_ACCESS_KEY_ID}"
        AWS_SECRET_ACCESS_KEY = "${params.AWS_SECRET_ACCESS_KEY}"
    }

    // agent {
    //     label 'ub-11'
    // }
    
    agent   any
    stages {
        stage('Checkout') {
            steps {
                 script{
                        dir("terraform")
                        {
                            git "https://github.com/paglipay/paglipay-terraform-jenkins.git"
                        }
                    }
                }
            }

        stage('Plan') {
            // steps {
            //     sh 'pwd;cd terraform/ ; terraform init'
            //     sh "pwd;cd terraform/ ; terraform plan -out tfplan"
            //     sh 'pwd;cd terraform/ ; terraform show -no-color tfplan > tfplan.txt'
            // }
            steps {
                sh 'terraform init'
                sh "terraform plan -out tfplan"
                sh 'terraform show -no-color tfplan > tfplan.txt'
            }
        }
        stage('Approval') {
           when {
               not {
                   equals expected: true, actual: params.autoApprove
               }
           }

           steps {
               script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
               }
           }
       }

        stage('Apply') {
            steps {
                sh 'terraform ${action} --auto-approve'
            }
            // steps {
            //     sh "pwd;cd terraform/ ; terraform apply -input=false tfplan"
            //     // sh "pwd;cd terraform/ ; terraform destroy"
            // }
        }
    }

  }
