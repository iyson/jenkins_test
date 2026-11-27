pipeline {
    agent any
    stages {
        stage('1. Checkout Code') {
            steps {
                echo 'GitHub에서 소스 코드를 성공적으로 가져왔습니다.'
            }
        }
        stage('2. Build') {
            steps {
                echo 'Maven JAR 빌드 진행 중...'
                sh 'docker run --rm -v "$WORKSPACE":/app -w /app/backend maven:3.9-eclipse-temurin-8 mvn clean package -DskipTests && ls -lh backend/target/*.jar'
            }
        }
        stage('3. Deploy') {
            steps {
                echo '서버로 JAR 배포 중...'
                sh 'mkdir -p /tmp/jar_deploy && cp backend/target/*.jar /tmp/jar_deploy/app.jar && ls -lh /tmp/jar_deploy/app.jar'
                echo '배포가 완료되었습니다!'
            }
        }
    }
}