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
				script {
					def jarFile = sh(script: 'ls backend/target/*.jar | head -n 1', returnStdout: true).trim()
					sh "sshpass -p '1234' scp -P 2222 -o StrictHostKeyChecking=no \"${jarFile}\" root@host.docker.internal:/opt/app/app.jar"
					sh "sshpass -p '1234' ssh -p 2222 -o StrictHostKeyChecking=no root@host.docker.internal 'PID=\$(ps -ef | grep app.jar | grep -v grep | awk \"{print \\\$2}\"); [ -n \"\$PID\" ] && kill -9 \$PID || true; nohup /opt/java8/bin/java -jar /opt/app/app.jar </dev/null >/opt/app/app.log 2>&1 &'"
				}
				echo '배포가 완료되었습니다!'
			}
		}
    }
}