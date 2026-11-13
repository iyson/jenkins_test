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
                echo '프로젝트 빌드 진행 중...'
                // 예: 프로젝트에 맞는 빌드 명령어 사용 (예: gradle clean build, npm install 등)
                // 현재는 테스트용으로 텍스트 파일을 하나 생성합니다.
                sh 'echo "Build Success at $(date)" > result.txt'
            }
        }
        stage('3. Deploy') {
			steps {
					echo '특정 사이트(서버)로 배포 중...'
					
					// 존재하지 않는 경로 대신 /tmp 폴더로 복사
					sh 'cp result.txt /tmp/'
					
					echo '배포가 완료되었습니다!'
			}
        }
    }
}