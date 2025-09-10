pipeline {
  agent any

  tools {
      jdk 'JDK17'
      maven 'Maven_3_9_11'
  }

  stages {

      stage('Check Java Version') {
          steps {
              bat 'java -version'
          }
      }

      stage('Compile and Run Sonar Analysis') {
          steps {
              withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
                  bat "mvn -Dmaven.test.failure.ignore verify sonar:sonar -Dsonar.login=%SONAR_TOKEN% -Dsonar.projectKey=easybuggy -Dsonar.host.url=http://localhost:9000/"
              }
          }
      }

    stage('Build Docker Image') {
      steps {
        withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
          script {
            app = docker.build("asecurityguru/testeb")
          }
        }
      }
    }

    stage('Run Container Scan (Snyk CLI)') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          script {
            try {
              bat "D:\\DevSecOps\\snyk.exe test --all-projects"
            } catch (err) {
              echo err.getMessage()
            }
          }
        }
      }
    }

    stage('Run Snyk SCA (Maven Plugin)') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          bat "mvn snyk:test -fn"
        }
      }
    }

    stage('Run DAST Using ZAP') {
      steps {
        bat "D:/Softwares/ZAP_2.16.0_Crossplatform/ZAP_2.16.0/zap.bat -cmd -quickurl https://www.example.com -quickprogress -quickout D:/Softwares/ZAP_2.16.0_Crossplatform/ZAP_2.16.0/Output.html"
      }
    }

    stage('Checkov (IaC Scan)') {
      steps {
        bat "checkov -s -f main.tf"
      }
    }

  }
}
