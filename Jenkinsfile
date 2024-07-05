pipeline{
    agent any
    stages{
        stage('Build Backend'){
            steps{
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage('Unit Tests'){
            steps{
               bat 'mvn test'
            }
        }
        stage('Sonar Analysis'){
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeplyBack  -Dsonar.host.url=http://localhost:9000 -Dsonar.login=d00091a4f37844722c0470bcc7f32a2200d09ed8 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
                }
            }
        }
        stage('Quality Gate'){
           steps{
           sleep(5)
           timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
               }
           }
        }
        stage('Deploy BackEnd'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage('API Test'){
            steps{
                dir('APITest') {
                     git credentialsId: 'a27fd5f8-54b9-4285-9181-6e40e73d0020', url: 'https://github.com/AnnaKarolineNunes/tasks-api-test'
                     bat 'mvn test'
                }
            }
        }
        stage('Deploy Frontend'){
            steps{
                dir('frontend'){
                    git credentialsId: 'a27fd5f8-54b9-4285-9181-6e40e73d0020', url: 'https://github.com/AnnaKarolineNunes/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage('Functional Test'){
            steps{
                dir('funtional-test'){
                    git credentialsId: 'a27fd5f8-54b9-4285-9181-6e40e73d0020', url: 'https://github.com/AnnaKarolineNunes/test-functional-test'
                    bat 'mvn test'
                }
            }
        }
    }
}

