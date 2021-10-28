node {
    stage("Git clone") {
        git url: 'https://github.com/Pomeo44/ms-ticket.git',  branch: '$BRANCH_NAME'
    }
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
    stage("Docker build"){
        withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
            sh 'docker login -u pomeo44 -p $PASSWORD'
        }
        sh 'docker build -t ms-ticket .'
        sh 'docker tag ms-ticket pomeo44/ms-ticket:$BRANCH_NAME-$BUILD_NUMBER'
        sh 'docker images'
    }
    stage("Docker push"){
        sh 'docker push pomeo44/ms-ticket:$BRANCH_NAME-$BUILD_NUMBER'
        try {
            sh 'docker rmi $(docker images | grep ms-ticket)'
        } catch (err) {
            echo err.getMessage()
        }
    }
    stage('Kubernetes deploy') {
        withKubeConfig([credentialsId: 'KUBERNETES_CREDENTIALS', serverUrl: '${CLUSTER_URL}', namespace: '${CLUSTER_NAMESPACE}']) {
            sh 'ls'
            sh 'helm upgrade ms-ticket ./helm --set container.image.tag=$BRANCH_NAME-$BUILD_NUMBER'
        }
    }
}
