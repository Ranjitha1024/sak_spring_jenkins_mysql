pipeline {
    agent any
    tools {
        maven 'maven'
    }
    environment {
        DEPLOY_DIR = "/home/ubuntu/springapp"
        JAR_NAME = "spring_app_sak-0.0.1-SNAPSHOT.jar"
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Deploy') {
            steps {
                sh """
                    echo "Stopping existing Spring Boot application if running..."
                    mkdir -p ${DEPLOY_DIR}

                    # Stop old app if running
                    if pgrep -f app.jar > /dev/null; then
                        sudo pkill -f app.jar
                        echo "Old application stopped."
                    else
                        echo "No existing application running."
                    fi

                    # Copy new JAR to deployment folder
                    cp target/${JAR_NAME} ${DEPLOY_DIR}/app.jar

                    # Start app in background
                    nohup java -jar ${DEPLOY_DIR}/app.jar > ${DEPLOY_DIR}/log.txt 2>&1 &
                    echo "Application started on port 8088"
                """
            }
        }
    }
    post {
        success {
            echo "Deployed successfully"
        }
        failure {
            echo "Failed to deploy"
        }
    }
}

