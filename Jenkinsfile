pipeline { // 파이프라인 전체 시작
    agent any // 빌드를 수행할 Jenkins 작업 에이전트 할당
    stages { // 실행할 단계(stage) 목록 시작
        stage('1. Checkout Code') { // 1단계: GitHub 소스코드 체크아웃 확인 단계
            steps { // 1단계에서 실행할 작업 블록
                echo 'GitHub에서 소스 코드를 성공적으로 가져왔습니다.' // 체크아웃 안내 로그 출력
            }
        }
        stage('2. Build') { // 2단계: 백엔드(Java 8) 및 프론트엔드(React) 빌드 단계
            steps { // 2단계에서 실행할 작업 블록
                echo '백엔드 JAR 및 프론트엔드 빌드 진행 중...' // 빌드 시작 로그 출력
                sh '''
                    export JAVA_HOME=/opt/java8 # Java 8 설치 경로 지정
                    export PATH="$JAVA_HOME/bin:$PATH" # Java 8 우선 사용
                    java -version # Java 8 버전 확인
                    mvn -f backend/pom.xml clean package -DskipTests # 백엔드 JAR 빌드
                    ls -lh backend/target/*.jar # 생성된 JAR 파일 확인
                '''
                sh "cd frontend && npm install && npm run build" // 프론트엔드 의존성 설치 및 정적 빌드
            }
        }
        stage('3. Deploy') { // 3단계: 백엔드(2222) 및 프론트엔드(2223) 배포 단계
            steps { // 3단계에서 실행할 작업 블록
                echo '리눅스 컨테이너로 백엔드/프론트엔드 배포 중...' // 배포 시작 로그 출력
                script { // 배포 명령 실행 블록
                    def jarFile = sh(script: 'ls backend/target/*.jar | head -n 1', returnStdout: true).trim() // 첫 번째 JAR 경로 추출
                    def frontDir = sh(script: '[ -d frontend/dist ] && echo frontend/dist || echo frontend/build', returnStdout: true).trim() // 빌드 산출물 폴더 판별
                    sh "sshpass -p '1234' scp -P 2222 -o StrictHostKeyChecking=no \"${jarFile}\" root@host.docker.internal:/opt/app/app.jar" // 백엔드 JAR를 deploy-server로 복사
                    sh "sshpass -p '1234' ssh -p 2222 -o StrictHostKeyChecking=no root@host.docker.internal 'PID=\$(ps -ef | grep app.jar | grep -v grep | awk \"{print \\\$2}\"); [ -n \"\$PID\" ] && kill -9 \$PID || true; nohup /opt/java8/bin/java -jar /opt/app/app.jar </dev/null >/opt/app/app.log 2>&1 &'" // 기존 백엔드 종료 후 재실행
                    sh "sshpass -p '1234' ssh -p 2223 -o StrictHostKeyChecking=no root@host.docker.internal 'rm -rf /var/www/html/* && mkdir -p /var/www/html'" // 기존 프론트 정적 파일 정리
                    sh "sshpass -p '1234' scp -P 2223 -o StrictHostKeyChecking=no -r ${frontDir}/* root@host.docker.internal:/var/www/html/" // 빌드된 정적 파일만 deploy-front로 복사
                    sh "sshpass -p '1234' ssh -p 2223 -o StrictHostKeyChecking=no root@host.docker.internal 'service nginx reload'" // Nginx 설정 및 정적 파일 반영
                }
                echo '백엔드와 프론트엔드 배포가 완료되었습니다!' // 배포 완료 로그 출력
            }
        }
    }
}