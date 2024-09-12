pipeline {
  agent any
  
  tools{
        maven 'maven3'
        }

  environment {
    SCANNER_HOME=tool 'sonar-scanner'
  }
  
  stages {
    stage('Git Checkout') {
      steps {
        // Git checkout
        //git branch: '<branch_name>', url: '<git_repository_url>'
        git branch: 'main', url: 'https://github.com/jaiswaladi246/Petclinic.git'
      }
    }
    
    stage('Maven Compile') {
      steps {
        // Maven compile
        sh 'mvn compile'
      }
    }
    
    stage('Maven test') {
      steps {
        // Maven compile
        sh 'mvn test'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('sonarqubeserver') {
            sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
            -Dsonar.java.binaries=. \
            -Dsonar.projectKey=petclinic '''
        }    
      }
    }

    stage('Maven Build') {
      steps {
        // Maven package
        sh 'mvn clean install'
      }
    }

    stage('OWASP Dependency Check') {
      steps {
        // OWASP Dependency Check
		    dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owaspcheck'
                  dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
      }
    }
    

    stage('Deploy to Tomcat') {
      steps {
        // Use Jenkins Credentials to securely pass username and password
            deploy adapters: [tomcat9(credentialsId: 'tomcat9cred', path: '', url: 'http://44.201.202.214:8080')], contextPath: '/petclinic', onFailure: false, war: '**/*.war'
      }    
    }

  }

}
