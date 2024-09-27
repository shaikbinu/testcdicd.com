pipeline {
    agent any
    environment {
        // Define environment variables for SSH
        UAT_SERVER = 'localhost'
        PROD_SERVER = '20.233.70.217'
        SSH_USER = 'root'
        HTML_PATH = '/var/www/html/testcdicd.com' // Change this to the path where the HTML files are hosted
    }

    stages {
        stage('Backup Production HTML Files') {
            steps {
                sh """
                ssh ${SSH_USER}@${PROD_SERVER} 'tar czf /tmp/prod_html_backup.tar.gz -C ${HTML_PATH} .'
                scp ${SSH_USER}@${PROD_SERVER}:/tmp/prod_html_backup.tar.gz .
                """
            }
        }

        stage('Copy HTML Files from UAT to Production') {
            steps {
                sh """
                ssh ${SSH_USER}@${UAT_SERVER} 'tar czf /tmp/uat_html_backup.tar.gz -C ${HTML_PATH} .'
                scp ${SSH_USER}@${UAT_SERVER}:/tmp/uat_html_backup.tar.gz .
                scp uat_html_backup.tar.gz ${SSH_USER}@${PROD_SERVER}:/tmp/
                ssh ${SSH_USER}@${PROD_SERVER} 'tar xzf /tmp/uat_html_backup.tar.gz -C ${HTML_PATH}'
                """
            }
        }

        stage('Restart Web Server on Production') {
            steps {
                sh """
                ssh ${SSH_USER}@${PROD_SERVER} 'sudo systemctl restart apache2'
                """
            }
        }
    }

    post {
        success {
            echo 'Deployment of HTML files to Production Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
