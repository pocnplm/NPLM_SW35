pipeline {
    agent any

    // 스크린샷에 있는 값들을 '기본값'으로 설정하여 입력창 유지
    parameters {
        // 1. POLARION_TOKEN (사진에 있는 긴 토큰 값)
        string(name: 'POLARION_TOKEN', 
               defaultValue: 'eyJraWQiOiIyMDljOGNlNS0wYTAwODNkZS0yMzhlZjBjYy04NDNhMDM5NCIsInR5cCI6IkpXVCIsImFsZyI6IlJTMjU2In0.eyJzdWIiOiJhZG1pbiIsImlkIjoiYWFjMWIxMWUtMGEwMDA5YWYtMmI0ZTI5ZDQtZmFjMTdhMjkiLCJleHAiOjE3NzMxNTQ4MDAsImlhdCI6MTc2ODA5NjM3OX0.ONS_D1V7g0d-Oqzmk81kzj0sDdbApHyVQqkTlfnZ8yD197NpL3O1Huv2ogmbrtQF8kjNg2mXl7KTdaORiw2QR-zpH8uX3WDHgxRRA2w4UMV_-7e2moVy4l6rOzBPeMP5JP6txKo-eK7N0x7fnNdosXIdAPFr0VVIKIxRKCHmjy9u5kGCHRS5kJAYBXFuIgMWpVop7b0xbVUnimwOzqSCh7LcuiG_JwiNpy2zotNmLNWUEtdM_917T8alpxFFPp3K0HPLOWMB3fvCyJoVx8fubPtNpmpuRI8I-O2YEOgPKkp5hpGQsfyBPU6Q2Fq5ZTt_7kZXHUkSihrzv9cucQmASg', 
               description: 'Polarion Access Token', 
               trim: true)
        
        // 2. projectid  (사진에 빈칸으로 되어 있음 -> 빈칸 유지)
        string(name: 'projectid', defaultValue: '', description: 'Project ID (e.g., elibrary)', trim: true)
        
        // 3. testRunId (사진에 빈칸으로 되어 있음 -> 빈칸 유지)
        string(name: 'testRunId', defaultValue: '', description: 'Target Test Run ID', trim: true)
        
        // 4. BASE_URL (사진에 있는 URL)
        string(name: 'BASE_URL', 
               defaultValue: 'http://3.35.188.230/polarion/rest/v1', 
               description: 'Polarion REST API URL', 
               trim: true)
        
        // 5. PDF_PATH (사진에 있는 경로)
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
                    // 빌드 스크립트 실행
                    bat 'python scripts/build_interface.py'
                }
            }
        }

        stage('Upload to Polarion') {
            steps {
                script {
                    echo ">>> [Stage 2] Uploading Result to Polarion..."
                    // 업로드 스크립트 실행 
                    // (위 파라미터들이 Python의 os.getenv로 자동 전달됨)
                    bat 'python scripts/upload_polarion.py'
                }
            }
        }
        stage('Update Test Records') {
            steps {
                script {
                    echo ">>> [Stage 3] Updating Test Records (Pass/Fail)..."
                    // 파이썬 스크립트 실행 (key.text 자동 인식)
                    bat 'python scripts/update_test_records.py'
                }
            }
        }
    }

    post {
        success {
            echo ">>> Pipeline Succeeded. Artifacts archived."
            archiveArtifacts artifacts: 'dist/*.zip', fingerprint: true
        }
        failure {
            echo ">>> Pipeline Failed."
        }
    }
}