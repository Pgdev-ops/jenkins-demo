pipeline {
    agent any

    environment {
        DEPLOY_DIR = "C:\\Deploy\\App"
        BACKUP_DIR = "C:\\Backup\\App_${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/Pgdev-ops/jenkins-demo.git'
            }
        }

        stage('Take Backup') {
            steps {
                bat """
                    echo Creating backup...
                    mkdir "%BACKUP_DIR%"
                    xcopy /E /I /Y "%DEPLOY_DIR%" "%BACKUP_DIR%"
                """
            }
        }

        stage('Deploy Builds') {
            steps {
                bat """
                    echo Deploying new build files...
                    xcopy /E /I /Y "builds\\" "%DEPLOY_DIR%"
                """
            }
        }

        stage('Run SQL Scripts') {
            steps {
                bat """
                    echo Simulating SQL script execution...
                    for %%f in (sql-scripts\\*.sql) do echo Running %%f
                    REM To actually run SQL scripts, configure sqlplus/sqlcmd
                """
            }
        }

        stage('Restart IIS') {
            steps {
                bat """
                    echo Restarting IIS...
                    iisreset
                """
            }
        }
    }
}
