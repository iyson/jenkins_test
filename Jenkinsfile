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
                sh 'mvn clean package -DskipTests && ls -lh target/*.jar'
            }
        }
        stage('3. Deploy') {
            steps {
                echo '서버로 JAR 배포 중...'
                sh 'mkdir -p /tmp/jar_deploy && cp target/*.jar /tmp/jar_deploy/app.jar'
                echo '배포가 완료되었습니다!'
            }
        }
    }
}