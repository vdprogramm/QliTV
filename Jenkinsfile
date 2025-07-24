pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning source code'
                git branch: 'main', url: 'https://github.com/vdprogramm/QliTV.git'
            }
        }

        stage('Restore Package') {
            steps {
                echo 'Restoring packages...'
                bat 'dotnet restore'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                bat 'dotnet test --no-build --verbosity normal'
            }
        }

        stage('Publish to folder') {
            steps {
                echo 'Publishing...'
                bat 'dotnet publish -c Release -o ./publish'
            }
        }

        stage('Copy to wwwroot') {
            steps {
                echo 'Copying published files to wwwroot'
                bat 'xcopy "%WORKSPACE%\\publish" "C:\\wwwroot\\myproject" /E /Y /I /R'
            }
        }

        stage('Deploy to IIS') {
            steps {
                powershell '''
                Import-Module WebAdministration
                if (-not (Test-Path IIS:\\Sites\\MySite)) {
                    New-Website -Name "MySite" -Port 81 -PhysicalPath "C:\\wwwroot\\myproject"
                }
                '''
            }
        }
    }
}
