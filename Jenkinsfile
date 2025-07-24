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
                bat 'dotnet publish -c Release -o publish'
            }
        }

        stage('Copy to wwwroot') {
            steps {
                echo 'Copying published files to wwwroot'
                bat '''
                    if not exist "C:\\wwwroot\\myproject" mkdir "C:\\wwwroot\\myproject"
                    xcopy /E /Y /I /R publish\\* "C:\\wwwroot\\myproject\\"
                '''
            }
        }

        stage('Deploy to IIS') {
            steps {
                echo 'Deploying to IIS'
                powershell '''
                    Import-Module WebAdministration
                    if (-not (Test-Path IIS:\\Sites\\MySite)) {
                        New-Website -Name "MySite" -Port 81 -PhysicalPath "C:\\wwwroot\\myproject" -ApplicationPool ".NET v4.5"
                    } else {
                        Set-ItemProperty IIS:\\Sites\\MySite -Name physicalPath -Value "C:\\wwwroot\\myproject"
                    }
                '''
            }
        }
    }
}
