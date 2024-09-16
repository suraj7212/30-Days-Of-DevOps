pipeline {
  agent any
  
  tools{
    maven 'maven3'
    jdk 'jdk17'
    }

  environment {
    SCANNER_HOME=tool 'sonar-scanner'
  }
  
  stages {
    stage('Git Checkout') {
      steps {
        // Git checkout
        //git branch: '<branch_name>', url: '<git_repository_url>'
        git branch: 'main', url: 'https://github.com/suraj7212/Petclinic.git'
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

    stage('Mvn deploy to nexus') {
      steps {
        configFileProvider([configFile(fileId: 'fc660202-6624-43fe-86f9-6258e60dc122', variable: 'mavensettings')]) {
        sh " mvn -s $mavensettings clean deploy"
        }
      }
    }
    
    stage('Docker Build') {
      steps {
        script{
          withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
            sh'''
            docker build -t petclinic .
            docker tag petclinic surajlavhale/petclinic
            '''
          }
        }
      }
    }

    stage('Docker Push') {
      steps {
        script{
          withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
            sh "docker push surajlavhale/petclinic"
          }
        }
      }
    }

    stage('Deploy To Container') {
      steps {
        script{
          withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
            sh "docker run -d --name petclinic -p 8070:8070 surajlavhale/petclinic"
          }
        }
      }
    }

  }

}






// pipeline {
//   agent any
  
//   tools{
//         maven 'maven3'
//         jdk 'jdk17'
//         }

//   environment {
//     SCANNER_HOME=tool 'sonar-scanner'
//   }
  
//   stages {
//     stage('Git Checkout') {
//       steps {
//         // Git checkout
//         //git branch: '<branch_name>', url: '<git_repository_url>'
//         git branch: 'main', url: 'https://github.com/jaiswaladi246/Petclinic.git'
//       }
//     }
    
//     stage('Maven Compile') {
//       steps {
//         // Maven compile
//         sh 'mvn compile'
//       }
//     }
    
//     stage('Maven test') {
//       steps {
//         // Maven compile
//         sh 'mvn test'
//       }
//     }

//     stage('SonarQube Analysis') {
//       steps {
//         withSonarQubeEnv('sonarqubeserver') {
//             sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Petclinic \
//             -Dsonar.java.binaries=. \
//             -Dsonar.projectKey=petclinic '''
//         }    
//       }
//     }

//     stage('Maven Build') {
//       steps {
//         // Maven package
//         sh 'mvn clean install'
//       }
//     }

//     stage('OWASP Dependency Check') {
//       steps {
//         // OWASP Dependency Check
// 		    dependencyCheck additionalArguments: '--scan target/', odcInstallation: 'owaspcheck'
//                   dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
//       }
//     }

//     stage('Mvn deploy to nexus') {
//       steps {
//         configFileProvider([configFile(fileId: 'e94f5fe3-3db8-4399-a661-bcedfd5d20fd', variable: 'mavensettings')]) {
//         sh " mvn -s $mavensettings clean deploy"
//         }
//       }
//     }
    
//     stage('Deploy to Tomcat') {
//       steps {
//         // Use Jenkins Credentials to securely pass username and password
//             deploy adapters: [tomcat9(credentialsId: 'tomcat9cred', path: '', url: 'http://44.201.202.214:8080')], contextPath: '/petclinic', onFailure: false, war: '**/*.war'
//       }    
//     }

//   }

// }
