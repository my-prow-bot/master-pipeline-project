pipeline {
    agent any

    stages {
        stage('Trigger Jobs in Parallel') {
            parallel {
                stage('Run Job A') {
                    steps { build job: 'job-A', parameters: [string(name: 'APP_VERSION', value: '2.0.0')] }
                }
                stage('Run Job B') {
                    steps { build job: 'job-B', parameters: [string(name: 'APP_VERSION', value: '2.0.0')] }
                }
                stage('Run Job C') {
                    steps { build job: 'job-C', parameters: [string(name: 'APP_VERSION', value: '2.0.0')] }
                }
            }
        }

        stage('Export Job Configs to YAML') {
            steps {
                script {
                    def jobs = ['job-A', 'job-B', 'job-C']
                    for (job in jobs) {
                        sh """
                            curl -u "\$JENKINS_USER:\$JENKINS_TOKEN" \\
                                 "\$JENKINS_URL/job/${job}/config.xml" -o ${job}.xml
                            yq eval ${job}.xml -P > ${job}.yaml
                        """
                    }
                }
            }
        }

        stage('Commit & Push YAML') {
            steps {
                sh '''
                    git config user.email "jenkins@ci.local"
                    git config user.name "Jenkins CI"
                    git add *.yaml
                    git commit -m "Update job configs"
                    git push origin HEAD:main
                '''
            }
        }
    }
}
