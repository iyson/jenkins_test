pipeline { // 파이프라인 전체 시작
    agent any // 빌드를 수행할 Jenkins 작업 에이전트 할당
    stages { // 실행할 단계(stage) 목록 시작
        stage('1. Checkout Code') { // 1단계: GitHub 소스코드 체크아웃 확인 단계
            steps { // 1단계에서 실행할 작업 블록
                echo 'GitHub에서 소스 코드를 성공적으로 가져왔습니다.' // 체크아웃 안내 로그 출력
            }
        }
        stage('2. Build') { // 2단계: Java 8 환경으로 JAR 파일 빌드 단계
            steps { // 2단계에서 실행할 작업 블록
                echo '오픈JDK 8로 Maven JAR 빌드 진행 중...' // 빌드 시작 로그 출력
                sh '''
                    export JAVA_HOME=/opt/java8 # Java 8 설치 경로를 JAVA_HOME으로 지정
                    export PATH="$JAVA_HOME/bin:$PATH" # Java 8 실행 파일이 우선 쓰이도록 PATH 설정
                    java -version # 적용된 Java 버전 로그 출력
                    mvn -f backend/pom.xml clean package -DskipTests # backend 프로젝트 정리 및 테스트 생략 JAR 빌드
                    ls -lh backend/target/*.jar # 생성된 JAR 파일 경로와 크기 확인
                '''
            }
        }
        stage('3. Deploy') { // 3단계: 빌드된 JAR 파일을 원격 서버에 전송 및 재실행 단계
            steps { // 3단계에서 실행할 작업 블록
                echo '리눅스 컨테이너로 JAR 배포 중...' // 배포 시작 로그 출력
                script { // Groovy 변수와 명령을 자유롭게 쓰기 위한 스크립트 블록
                    def jarFile = sh(script: 'ls backend/target/*.jar | head -n 1', returnStdout: true).trim() // 첫 번째 JAR 파일 경로를 공백 제거 후 저장
                    sh "sshpass -p '1234' scp -P 2222 -o StrictHostKeyChecking=no \"${jarFile}\" root@host.docker.internal:/opt/app/app.jar" // 비밀번호 1234로 2222 포트 접속해 JAR 파일 복사
                    sh "sshpass -p '1234' ssh -p 2222 -o StrictHostKeyChecking=no root@host.docker.internal 'PID=\$(ps -ef | grep app.jar | grep -v grep | awk \"{print \\\$2}\"); [ -n \"\$PID\" ] && kill -9 \$PID || true; nohup /opt/java8/bin/java -jar /opt/app/app.jar </dev/null >/opt/app/app.log 2>&1 &'" // 기존 app.jar PID 종료 후 새 JAR를 백그라운드 로그 기록으로 실행
                }
                echo '배포가 완료되었습니다!' // 배포 완료 로그 출력
            }
        }
    }
}