pipeline {
    agent any

    // 1. CORRECTION DES VARIABLES : Déplacement dans la section 'environment'
    environment {
        // Accès dans les steps via 'env.NOM_VARIABLE'
        REPOSITORY_URL = 'https://github.com/yakoulou/HelloWorldMaven'
        // TRÈS IMPORTANT : Remplacez cette valeur par l'ID exact de votre credential Jenkins
        CREDENTIALS_ID = 'habib' // <--- REMPLACER PAR VOTRE VRAI ID TROUVÉ DANS JENKINS
        GIT_EMAIL = 'habib.benmansour11314@gmail.com'
        GIT_NAME = 'yakoulou'
        TARGET_REMOTE = 'Habib' 
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning repository...'
                // Utilisation des variables d'environnement
                git(
                    url: env.REPOSITORY_URL, 
                    credentialsId: env.CREDENTIALS_ID,
                    branch: 'master' // Généralement plus simple que '*/master'
                )
            }
        }

        stage('Build & Configure Git') {
            steps {
                echo 'Running build and configuring local Git...'

                sh """
                git config --global user.email ${env.GIT_EMAIL}
                git config --global user.name ${env.GIT_NAME}
                
                mvn clean install
                """
            }
        }
    }

    // --- Bloc POST : Exécution après les stages principaux ---
    post {
        // S'exécute uniquement si TOUS les stages précédents ont réussi
        success {
            // 2. CORRECTION DU BLOC POST : Remplacement du 'stage' par un bloc 'script'
            script {
                // Définition de la variable de Tag ici, où ${BUILD_NUMBER} est disponible
                def TAG_TO_PUSH = "V-3.0.${env.BUILD_NUMBER}"
                
                echo "Build successful. Pushing tag: ${TAG_TO_PUSH} to remote: ${env.TARGET_REMOTE}"
                
                // Création du tag localement (-f pour 'Update new tag')
                sh "git tag -f -a ${TAG_TO_PUSH} -m 'Tag created by Jenkins build ${env.BUILD_NUMBER}'"
                
                // Push du tag sécurisé
                withCredentials([usernamePassword(credentialsId: env.CREDENTIALS_ID, usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                    // Utilisation de l'URL pour un push direct avec les credentials
                    sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@${env.REPOSITORY_URL.substring(8)} ${TAG_TO_PUSH} -f"
                }
            }
        }
    }
}
