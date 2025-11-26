pipeline {
    agent any
    
    environment {
        DEPLOY_DIR = "/home/ubuntu/springapp"   // Deployment folder
        APP_JAR = "app.jar"                     // Name of the JAR in deployment folder
        APP_PORT = "8088"                       // Spring Boot port
        REPO_URL = "https://github.com/ranjitha1024/sak_spring_jenkins_mysql.git" // Private repo
    }

    tools {
        maven 'maven'   // Make sure Maven is configured in Jenkins Global Tool Configuration
        jdk 'Java17'    // Make sure Java 17 is configured in Jenkins
    }

    stages {
        stage('Checkout SCM') {
            steps {
                echo "Checking out code from private GitHub repo..."
                git branch: 'main', url: "${REPO_URL}", credentialsId: 'YOUR_GITHUB_CREDENTIAL_ID'
            }
        }

        stage('Build') {
            steps {
                echo "Building Spring Boot application with Maven..."
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying application..."
                sh '''
                    # Create deployment folder if it doesn't exist
                    mkdir -p ${DEPLOY_DIR}

                    # Stop old app if running
                    if pgrep -f ${APP_JAR} > /dev/null; then
                        pkill -f ${APP_JAR}
                        echo "Old application stopped."
                    else
                        echo "No existing application running."
                    fi

                    # Copy new JAR to deployment folder
                    cp target/spring_app_sak-0.0.1-SNAPSHOT.jar ${DEPLOY_DIR}/${APP_JAR}

                    # Start app in background with logs
                    nohup java -jar ${DEPLOY_DIR}/${APP_JAR} > ${DEPLOY_DIR}/log.txt 2>&1 &
                    echo "Application started on port ${APP_PORT}"
                '''
            }
        }
    }

    post {
        success {
            echo "Pipeline finished successfully. Application deployed."
        }
        failure {
            echo "Pipeline failed. Check logs for errors."
        }
    }
}
