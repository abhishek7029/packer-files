pipeline {
    agent any

    parameters {
        // Select Recommender Environment
        choice(
                name: 'ENVIRONMENT',
                choices: "PROD-RA\nPROD-RB\nPROD-RC\nPROD-RD\nPROD-RE",
                description: 'Select the recommender environment'
        )
        // Select AWS region
        choice(
                name: 'AWS_REGION',
                choices: "us-east-1",
                description: 'Select an AWS region for recommender'
        )

        // AMI Name
        string(
            name: 'AMI_NAME', 
            description: 'Enter a name for the AMI', 
            defaultValue: ''
        )

        // Current latest AMI ID 
        string(
            name: 'AMI_ID', 
            description: 'Enter current latest AMI ID for the selected environment', 
            defaultValue: ''
        )
        
        booleanParam(
            name: 'nginx',
            description: 'Install nginx',
            defaultValue: false 
        )

        booleanParam(
            name: 'nodejs',
            description: 'Install nodejs',
            defaultValue: false 
        )

        booleanParam(
            name: 'mysql',
            description: 'Install mysql',
            defaultValue: false 
        )

        booleanParam(
            name: 'apache2',
            description: 'Install apache2',
            defaultValue: false 
        )

        booleanParam(
            name: 'ALL',
            description: 'Install / Update all softwares and security patches',
            defaultValue: false 
        )
        
    }

    stages {

     

        stage('Clone Packer Repo') {
            steps {
                dir ("${WORKSPACE}/${BUILD_ID}") {
                    sh '''
                    git clone https://github.com/abhishek7029/packer-files.git
                    '''
                }
                }
            }

        stage('making file ') {
            steps {
                dir ("${WORKSPACE}/${BUILD_ID}") {
                sh '''
                #!/bin/bash
                echo ' #!/bin/bash ' >> file.sh
                '''
                }
            }
            
        }

        stage('Build AMI with nginx') {
            when {
                expression {
                    nginx == 'true' || ALL == 'true'
                }
            }
            steps {
                dir ("${WORKSPACE}/${BUILD_ID}") {
                sh '''
                #!/bin/bash
                if [ $nginx = "true" ] || [ $ALL = "true" ]
                then
                    echo "sudo apt-get install -y nginx" >> file.sh                 
                else
                    not building AMI
                fi
                '''
                }
            }
        }

        stage('Build AMI with nodejs') {
            when {
                expression {
                    nodejs == 'true'  || ALL == 'true'
                }
            }
            steps {
                dir ("${WORKSPACE}/${BUILD_ID}") {
                sh '''
                #!/bin/bash
                if [ $nodejs = "true" ] || [ $ALL = "true" ]
                then
                    echo "sudo apt-get install -y nodejs" >> file.sh
                else
                    not building AMI
                fi
                '''
                }
            }
        }

        stage('Build AMI with mysql') {
            when {
                expression {
                    mysql == 'true'  || ALL == 'true'
                }
            }
            steps {
                dir ("${WORKSPACE}/${BUILD_ID}") {
                sh '''
                #!/bin/bash
                if [ $mysql = "true" ] || [ $ALL = "true" ]
                then
                    echo "sudo apt-get install -y mysql-server" >> file.sh
                else
                    not building AMI
                fi
                '''
                }
            }
        }

        stage('Build AMI with apache') {
            when {
                expression {
                    apache2 == 'true'  || ALL == 'true'
                }
            }
            steps {
                dir ("${WORKSPACE}/${BUILD_ID}") {
                sh '''
                #!/bin/bash
                if [ $apache2 = "true" ] || [ $ALL = "true" ]
                then
                    echo "sudo apt-get install -y apache2" >> file.sh
                else
                    not building AMI
                fi
                '''
                }
            }
        }

        stage('Build AMI ') {
            steps {
                dir ("${WORKSPACE}/${BUILD_ID}") {
                sh '''
                #!/bin/bash
                echo building AMI with all softwares and patches
                # Init
                packer init ./packer-files/main.pkr.hcl
                    
                # FMT
                packer fmt ./packer-files/main.pkr.hcl
                    
                # Validate
                packer validate  ./packer-files/main.pkr.hcl
                '''
                }
            }
        }

        // stage('Fetch AMI ID') {
        //     steps {
        //         dir ("${WORKSPACE}/${BUILD_ID}") {
        //         sh '''
        //         #!/bin/bash
        //         echo I will fetch AMI ID from manifest.json
        //         '''
        //         }
        //     }
        // }
    }
    post {
        success {
            echo 'I will run after a successful build'
        }
        failure {
            echo "I will run if the pipeline fails completely"
        }
        always {
            echo "I will always execute no matter what!"
            sh 'df -Th'
            cleanWs()
        }
    }
}
