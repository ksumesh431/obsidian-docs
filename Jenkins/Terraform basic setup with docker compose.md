``` Dockerfile
FROM jenkins/jenkins:lts-jdk17

USER root

# Install required packages
RUN apt-get update && apt-get install -y gnupg software-properties-common wget

# Add HashiCorp GPG key
RUN wget -O- https://apt.releases.hashicorp.com/gpg | \
    gpg --dearmor | \
    tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null

# Add HashiCorp repository
RUN echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
    https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
    tee /etc/apt/sources.list.d/hashicorp.list

# Install Terraform
RUN apt-get update && apt-get install -y terraform

USER jenkins


```

``` yml
services:
  jenkins:
    build: .
    ports:
      - "8000:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
    container_name: jenkins
    restart: on-failure
volumes:
  jenkins_home:
    driver: local

```

`docker compose up --build -d`

- ~~Install aws credential provider and set up aws creds / or can just attach the required role if runnign in aws~~
- In credentials, set up secret text type, and set AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY.
- install terraform jenkins plugin
- set up tf jenkins plugin by setting the correcrt path on host machine (make sure tf is present on host machine already)
- create job (pipeline)
``` groovy
pipeline {
    environment {
        AWS_ACCESS_KEY_ID = credentials("AWS_ACCESS_KEY_ID")
        AWS_SECRET_ACCESS_KEY = credentials("AWS_SECRET_ACCESS_KEY")
    }
    agent any
    tools {
        terraform 'terraform'
    }
    parameters {
        booleanParam(name: 'DO_DESTROY', defaultValue: false, description: 'Set to true to run terraform destroy instead of apply')
    }
    stages {
        stage("Git Checkout") {
            steps {
                git branch: 'main', url: 'https://github.com/ksumesh431/tf-jenkins-test'
            }
        }
        stage("Terraform Init") {
            steps {
                sh 'terraform init'
            }
        }
        stage("Terraform Plan or Apply/Destroy") {
            steps {
                script {
                    if (params.DO_DESTROY) {
                        echo 'Running terraform destroy...'
                        sh 'terraform destroy --auto-approve'
                    } else {
                        echo 'Running terraform plan and apply...'
                        sh 'terraform plan'
                        sh 'terraform apply --auto-approve'
                    }
                }
            }
        }
    }
}


```