pipeline {
    agent any

    tools {
        jdk 'OpenJDK-21'          // use your JDK name from Jenkins Tools
        maven 'Maven-Auto'        // or SystemMaven if manually installed
    }

    environment {
        APP_NAME = 'springboot-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Check JAVA_HOME') {
            steps {
                sh 'echo $JAVA_HOME'
                sh '$JAVA_HOME --version'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the application...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging application JAR...'
                sh 'ls -l target/*.jar'
            }
        }

        stage('Deploy to Podman') {
            steps {
                echo 'Deploying app to Podman container...'
                sh '''
                podman build -t $APP_NAME .
                podman stop $APP_NAME || true
                podman rm $APP_NAME || true
                podman run -d --name $APP_NAME -p 8080:8080 $APP_NAME
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Build and deployment completed successfully!'
        }
        failure {
            echo '❌ Build failed. Check console logs.'
        }
    }
}