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
				echo '오픈JDK 8로 Maven JAR 빌드 진행 중...'
				sh '''
					export JAVA_HOME=/opt/java8
					export PATH="$JAVA_HOME/bin:$PATH"
					java -version
					mvn -f backend/pom.xml clean package -DskipTests
					ls -lh backend/target/*.jar
				'''
			}
		}
		stage('3. Deploy') {
			steps {
				echo '리눅스 컨테이너로 JAR 배포 중...'
				withCredentials([usernamePassword(credentialsId: 'deploy-server-ssh', usernameVariable: 'SSH_USER', passwordVariable: 'SSH_PASS')]) {
					script {
						def jarFile = sh(script: 'ls backend/target/*.jar | head -n 1', returnStdout: true).trim()
						sh "sshpass -p \"$SSH_PASS\" scp -P 2222 -o StrictHostKeyChecking=no \"${jarFile}\" \"$SSH_USER@host.docker.internal:/opt/app/app.jar\""
						sh "sshpass -p \"$SSH_PASS\" ssh -p 2222 -o StrictHostKeyChecking=no \"$SSH_USER@host.docker.internal\" '/opt/app/run.sh'"
					}
				}
				echo '배포가 완료되었습니다!'
			}
		}
    }
}