pipeline {
    agent any
    
    environment{
        scannerHome = tool name: 'sonar_scanner_dotnet'
    }

    stages {
        stage('Checkout Code') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'Github', url: 'https://github.com/yashdhanwantri/app_yashdhanwantri.git']]])
            }
        }
        stage('Nuget restore') {
            steps {
                bat 'dotnet restore'
            }
        }
        
        stage('Start sonarqube analysis') {
            steps {
                echo 'Starting Sonarqube Analysis'
                withSonarQubeEnv('Test_Sonar'){
                    bat "dotnet ${scannerHome}\\SonarScanner.MSBuild.dll begin /k:\"sonar-yashdhanwantri\" /d:sonar.verbose=true"
                }
            }
        }
        stage('Code build') {
            steps {
                bat 'dotnet clean'
                bat 'dotnet build'
            }
        }
        
        stage('Test case execution'){
            steps{
                bat 'dotnet test test-project\\test-project.csproj'
            }
        }
        
        stage('Stop sonarqube analysis') {
            steps {
                echo "Stopping sonarqube analysis"
                withSonarQubeEnv('Test_Sonar'){
                    bat "dotnet ${scannerHome}\\SonarScanner.MSBuild.dll end"
                }
            }
        }
        stage('Release artifact') {
            steps{
                bat 'dotnet publish'
            }
        }
    }
}
