pipeline {
    agent any 

    environment {
        ANSIBLE_REPO = '/var/lib/jenkins/workspace/ansible_master'
        PRD_REPO = '/var/lib/jenkins/workspace/Swarm_Lab_master'
        DEV_REPO = '/var/lib/jenkins/workspace/Swarm_Lab_dev_test'
        WEBHOOK = credentials('JENKINS_DISCORD')
        PORTAINER_DEV_WEBHOOK = credentials('PORTAINER_WEBHOOK_DEV_PRIVATEER')
        PORTAINER_PRD_WEBHOOK = credentials('PORTAINER_WEBHOOK_PRD_PRIVATEER')
    }

    //triggering periodically so the code is always present
    // run every friday at 3AM
    triggers { cron('0 3 * * 5') }

    stages {
        // deploy code to DEV, when the branch is 'dev_test'
        stage('deploy DEV code') {
            when { branch 'dev_test' }
            steps {
                // deploy configs to DEV
                echo 'deploy docker config files (DEV)'
                sh 'ansible-playbook ${DEV_REPO}/deploy/deploy_vespae.yml'
                sh 'http post ${PORTAINER_DEV_WEBHOOK}'
            }
        }

        // deploy code to PRD, when the branch is 'master'
        stage('deploy PRD code') {
            when { branch 'master' }
            steps {
                // deploy configs to PRD
                echo 'deploy docker config files (PRD)'
                sh 'ansible-playbook ${PRD_REPO}/deploy/deploy_apis.yml'
                sh 'http post ${PORTAINER_PRD_WEBHOOK}'
            }
        }

    }
    post {
        always {
            discordSend \
                description: "${JOB_NAME} - build #${BUILD_NUMBER}", \
                // footer: "Footer Text", \
                // link: env.BUILD_URL, \
                result: currentBuild.currentResult, \
                // title: JOB_NAME, \
                webhookURL: "${WEBHOOK}"
        }
    }
}

