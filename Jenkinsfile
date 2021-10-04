node {
    stage('Maven build') {
        withMaven (maven: 'maven') {
            sh 'mvn -B -DskipTests clean package'
        }
    }
    stage('Maven test') {
        withMaven (maven: 'maven') {
            sh 'mvn test'
        }
    }
    stage("Docker login"){
        withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
            sh 'docker login -u pomeo44 -p $PASSWORD'
        }
    }
    stage("Docker build"){
        sh 'docker version'
        sh 'docker build -t ms-ticket .'
        sh 'docker tag ms-ticket pomeo44/ms-ticket:jenkins'
        sh 'docker tag ms-ticket pomeo44/ms-ticket:$BUILD_NUMBER'
        sh 'docker images'
    }
    stage("Docker push"){
        sh 'docker push pomeo44/ms-ticket -a'
    }
    stage('Kubernetes deploy') {
        withKubeConfig([credentialsId: 'KUBERNETES_CREDENTIALS', serverUrl: "${CLUSTER_URL}", namespace: "${CLUSTER_NAMESPACE}"]) {
            sh 'kubectl apply -f deployment.yaml'
        }
    }
}