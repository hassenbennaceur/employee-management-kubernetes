pipeline {
    /* On dit à Jenkins d'exécuter le pipeline sur n'importe quel agent.
       ⚠ Cet agent doit avoir Docker installé et le droit d'exécuter "docker build / docker push". */
    agent any

    /* Variables globales */
    environment {
        // Nom local de l'image 
        backend_image = "backend_cont"
        frontend_image = "front_cont"
        
        // Repo folders
        
        backendF = "emp_backend"
        frontendF = "emp_frontend"

        // URL du repo GitHub
        GIT_REPO = "https://github.com/hassenbennaceur/employee-management-docker.git"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "==> Récupération du code source depuis GitHub"

                /* Clone le repo avec tes identifiants Jenkins ''
                   - branch: mets 'main' ou 'master' selon ta branche */
                git branch: 'master',
                    credentialsId: 'github-cred',
                    url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image emp -- backend') {
            steps {
                echo "==> Build de l'image Docker locale"

                /* On construit l'image Docker en tag 'latest'
                   Le Dockerfile doit être à la racine du repo */
                sh """
                    docker build -t ${backend_image}:v1.0 ${backendF}   
                """
            }
        }

        stage('Push Docker Image emp -- backend') {
            steps {
                echo "==> Push de l'image sur Docker Hub (tag: latest)"

                /* On utilise les identifiants Jenkins ''
                   ATTENTION :
                   - usernameVariable = ton username Docker Hub
                   - passwordVariable = le mot de passe / token Docker Hub
                */
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-cred',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )]) {

                        // login Docker Hub
                        sh """
                            echo "${DOCKERHUB_PASS}" | docker login -u "${DOCKERHUB_USER}" --password-stdin
                        """

                        // retag l'image locale avec ton namespace Docker Hub
                        sh """
                            docker tag ${backend_image}:v1.0 ${DOCKERHUB_USER}/${backend_image}:v1.0
                            docker push ${DOCKERHUB_USER}/${backend_image}:v1.0
                        """

                        // logout 
                        sh "docker logout"
                    }
                }
            }
        }

        stage('Build Docker Image emp -- frontend') {
            steps {
                echo "==> Build de l'image Docker locale"

                /* On construit l'image Docker en tag 'latest'
                   Le Dockerfile doit être à la racine du repo */
                sh """
                    docker build -t ${frontend_image}:v1.0 ${frontendF}
                """
            }
        }

        stage('Push Docker Image emp -- frontend') {
            steps {
                echo "==> Push de l'image sur Docker Hub (tag: latest)"

                /* On utilise les identifiants Jenkins ''
                   ATTENTION :
                   - usernameVariable = ton username Docker Hub
                   - passwordVariable = le mot de passe / token Docker Hub
                */
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-cred',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )]) {

                        // login Docker Hub
                        sh """
                            echo "${DOCKERHUB_PASS}" | docker login -u "${DOCKERHUB_USER}" --password-stdin
                        """

                        // retag l'image locale avec ton namespace Docker Hub
                        sh """
                            docker tag ${frontend_image}:v1.0 ${DOCKERHUB_USER}/${frontend_image}:v1.0
                            docker push ${DOCKERHUB_USER}/${frontend_image}:v1.0
                        """

                        // logout 
                        sh "docker logout"
                    }
                }
            }
        }
        stage(' run docker compose') {
            steps {
                echo "==>   echo run docker compose "
                script {
                sh 
                    """
                    docker compose up -d
                    """
                 }
            }

        }

    }
    post {
        always {
            echo "Pipeline terminé."
        }
    }
}