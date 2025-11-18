pipeline {
    agent any

    tools {
        maven "M3"
    }

    stages {
        stage('Checkout') {
            steps {
                // Cloner le dépôt Git
                git branch: 'master',
                    url: 'https://github.com/RezzagYoussouf/HelloWorldMaven.git',
                    credentialsId: 'maven'
            }
        }

        stage('Build with Maven') {
            steps {
                // Lancer le build Maven
                sh "mvn clean install"
            }
        }
    }

    post {
        success {
            script {
                // Définir le nom du tag
                def TAG_NAME = "V-3.${env.BUILD_NUMBER}"

                // Configurer l'utilisateur Git pour le tagging
                sh "git config --global user.email 'youssouf.rezzag@gmail.com'"
                sh "git config --global user.name 'RezzagYoussouf'"

                // Créer le tag
                def tagCreationStatus = sh(
                    script: "git tag -a ${TAG_NAME} -m 'Tag créé par Jenkins pipeline après build Maven'",
                    returnStatus: true
                )

                // Vérifier si la création du tag a réussi
                if (tagCreationStatus == 0) {
                    // Pousser le tag sur le dépôt distant avec les credentials
                    withCredentials([usernamePassword(
                        credentialsId: 'maven',
                        passwordVariable: 'GIT_PASSWORD',
                        usernameVariable: 'GIT_USERNAME'
                    )]) {
                        sh "git push https://\\${GIT_USERNAME}:\\${GIT_PASSWORD}@github.com/RezzagYoussouf/HelloWorldMaven.git ${TAG_NAME}"
                    }
                } else {
                    // En cas d'échec de la création du tag
                    error "La création du tag a échoué. Le tag n'a pas été poussé."
                }
            }
        }

        failure {
            // Si la build échoue, afficher un message d'erreur
            echo "La build ou la création du tag a échoué."
        }
    }
}
