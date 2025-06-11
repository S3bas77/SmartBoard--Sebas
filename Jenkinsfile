pipeline {
    agent any

    environment {
        DROPBOX_TOKEN = credentials('DROPBOX_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/S3bas77/SmartBoard--Sebas.git'
            }
        }

        stage('Build APK') {
            steps {
                bat '.\\gradlew assembleDebug'
            }
        }

        stage('Upload to Dropbox') {
            steps {
                script {
                    def apkPath = "app\\build\\outputs\\apk\\debug\\app-debug.apk"
                    def dropboxDest = "/SmartBoard/app-debug.apk"

                    // Solo se intenta subir si hay token
                    bat """
                    if not "%DROPBOX_TOKEN%"=="" (
                        curl -X POST https://content.dropboxapi.com/2/files/upload ^
                            --header "Authorization: Bearer %DROPBOX_TOKEN%" ^
                            --header "Dropbox-API-Arg: {\\\"path\\\": \\\"${dropboxDest}\\\",\\\"mode\\\":\\\"overwrite\\\"}" ^
                            --header "Content-Type: application/octet-stream" ^
                            --data-binary @${apkPath}
                    ) else (
                        echo No DROPBOX_TOKEN found. Skipping upload.
                    )
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed.'
        }
        failure {
            echo 'Something went wrong.'
        }
    }
}
