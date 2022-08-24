pipeline {
    agent any
    
    environment{
        scannerHome = tool name: 'sonar_scanner_dotnet'
    }

    stages {
        stage('Nuget restore') {
            steps {
                bat 'dotnet restore'
            }
        }
        
        stage('Start sonarqube analysis') {
			when {
				branch 'master'
			}
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
			when {
				branch 'master'
			}
            steps{
                bat 'dotnet test test-project\\test-project.csproj'
            }
        }
        
        stage('Stop sonarqube analysis') {
			when {
				branch 'master'
			}
            steps {
                echo "Stopping sonarqube analysis"
                withSonarQubeEnv('Test_Sonar'){
                    bat "dotnet ${scannerHome}\\SonarScanner.MSBuild.dll end"
                }
            }
        }
        stage('Release artifact') {
			when {
				branch 'develop'
			}
            steps{
                bat 'dotnet publish'
            }
        }
		stage('Kubernetes Deployment') {
            steps{
				script {
                    if (env.BRANCH_NAME == 'master') {
                        bat 'kubectl apply -f ./Deployment-Master.yaml'
                    } else if (env.BRANCH_NAME == 'develop'){
                        bat 'kubectl apply -f ./Deployment-Dev.yaml'
                    }
                }
            }
        }
    }
}
