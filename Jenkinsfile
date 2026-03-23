node {

    environment {
        MINIKUBE_HOME = "/var/lib/jenkins/.minikube"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"
    }

    def mvnHome = tool name: 'maven', type: 'maven'
    def mvnCMD = "${mvnHome}/bin/mvn"

    stage('Checkout') {
        checkout([$class: 'GitSCM',
            branches: [[name: '*/main']],
            userRemoteConfigs: [[
                credentialsId: 'git',
                url: 'https://github.com/mohitpatil8990/service-registry.git'
            ]]
        ])
    }

    stage('Build Jar') {
        sh "${mvnCMD} clean package -DskipTests"
    }

    stage('Build Docker Image') {
        sh """
            eval \$(minikube docker-env)
            docker build -t eurekaserver:latest .
        """
    }

    stage('Deploy to Kubernetes') {
        sh """
            kubectl apply -f k8s/deployment.yaml
            kubectl rollout restart deployment eureka-server-app
        """
    }
}