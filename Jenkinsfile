pipeline {
    agent any

    environment{
        STAGING_DOMAIN = 'chris-todobem-staging.surge.sh' // variable publique
        PRODUCTION_DOMAIN = 'chris-todobem.surge.sh' // variable publique
        SURGE_LOGIN = credentials('jenkins-surge-login') // variable privée à définir dans Jenkins => Manage Jenkins => Manage Credentials
        SURGE_TOKEN = credentials('jenkins-surge-token') // variable privée à définir dans Jenkins => Manage Jenkins => Manage Credentials
    }
    stages{
        stage('build'){
            steps{
                sh 'npm install'
                sh 'npm run build'
                sh 'test -f public/index.html'
            }
        }
        stage('test'){
            steps{
                sh 'grep "Bravo" public/index.html'
            }
        }
        stage('deploy staging'){ // phase exécutée uniquement si on est sur la branche 'main'
            when {
                branch 'main'
            }
            steps{
                sh 'echo "Deploy to" $STAGING_DOMAIN'
                sh 'npm install -g surge'
                sh 'surge --project public --domain $STAGING_DOMAIN'
            }
        }
        stage('deploy prod'){
            input 'Deploy to prod?' // phase exécutée uniquement si l'utilisateur accepte
            steps{
                sh 'echo "Deploy to" $PRODUCTION_DOMAIN'
                sh 'npm install -g surge'
                sh 'surge --project public --domain $PRODUCTION_DOMAIN'
            }
        }
    }
    post {
        always {
            archiveArtifacts: 'public/*'
        }
    }
}