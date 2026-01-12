pipeline {
    agent any

    // 전역 변수 설정
    environment {
        ARTIFACT_NAME = "sys-monitor-1.0.0-POC"
    }

    parameters {
        string(name: 'POLARION_TOKEN', 
               defaultValue: 'eyJraWQiOiIyMDljOGNlNS0wYTAwODNkZS0yMzhlZjBjYy04NDNhMDM5NCIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiJhZG1pbiIsImlkIjoiYWFjMWIxMWUtMGEwMDA5YWYtMmI0ZTI5ZDQtZmFjMTdhMjkiLCJleHAiOjE3NzMxNTQ4MDAsImlhdCI6MTc2ODA5NjM3OX0.ONS_D1V7g0d-Oqzmk81kzj0sDdbApHyVQqkTlfnZ8yD197NpL3O1Huv2ogmbrtQF8kjNg2mXl7KTdaORiw2QR-zpH8uX3WDHgxRRA2w4UMV_-7e2moVy4l6rOzBPeMP5JP6txKo-eK7N0x7fnNdosXIdAPFr0VVIKIxRKCHmjy9u5kGCHRS5kJAYBXFuIgMWpVop7b0xbVUnimwOzqSCh7LcuiG_JwiNpy2zotNmLNWUEtdM_917T8alpxFFPp3K0HPLOWMB3fvCyJoVx8fubPtNpmpuRI8I-O2YEOgPKkp5hpGQsfyBPU6Q2Fq5ZTt_7kZXHUkSihrzv9cucQmASg', 
               description: 'Polarion Access Token', 
               trim: true)
        
        string(name: 'projectid', defaultValue: '', description: 'Project ID (e.g., elibrary)', trim: true)
        string(name: 'testRunId', defaultValue: '', description: 'Target Test Run ID', trim: true)
        
        string(name: 'BASE_URL', 
               defaultValue: 'http://3.35.188.230/polarion/rest/v1', 
               description: 'Polarion REST API URL', 
               trim: true)
        
        string(name: 'PDF_PATH', 
               defaultValue: 'C:\\Siemens\\Polarion\\Static Analysis.pdf', 
               description: 'Absolute path to PDF file', 
               trim: true)

        string(name: 'planType', 
               defaultValue: '', description: 'Plan Type(e.g., Agile)', trim: true)
    }

    stages {
        stage('Initialize') {
            steps {
                echo ">>> Initializing Pipeline..."
                checkout scm
            }
        }

        stage('Build & Package') {
            steps {
                script {
                    echo ">>> [Stage 1] Executing Build Script..."
                    // 1. 빌드 스크립트 실행 (dist 폴더에 zip이 생성된다고 가정)
                    bat 'python scripts/build_interface.py'

                    // 2. 생성된 산출물 이름을 고정된 이름으로 변경 (Windows bat 기준)
                    // dist 폴더 내의 어떤 zip 파일이든 고정 이름으로 변경합니다.
                    echo ">>> Renaming artifact to ${env.ARTIFACT_NAME}.zip"
                    bat "if exist dist\\*.zip move /y dist\\*.zip dist\\${env.ARTIFACT_NAME}.zip"
                }
            }
        }

        stage('Upload to Polarion') {
            steps {
                script {
                    echo ">>> [Stage 2] Uploading Result to Polarion..."
                    bat 'python scripts/upload_polarion.py'
                }
            }
        }

        stage('Update Test Records') {
            steps {
                script {
                    echo ">>> [Stage 3] Updating Test Records (Pass/Fail)..."
                    bat 'python scripts/update_test_records.py'
                }
            }
        }
    }

    post {
        success {
            echo ">>> Pipeline Succeeded. Archiving fixed name artifact."
            // 고정된 이름의 파일만 아카이브
            archiveArtifacts artifacts: "dist/${env.ARTIFACT_NAME}.zip", fingerprint: true
        }
        failure {
            echo ">>> Pipeline Failed."
        }
    }
}
